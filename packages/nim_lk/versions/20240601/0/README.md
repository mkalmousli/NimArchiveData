# Nim lockfile generator

Generates Nix specific lockfiles for Nim packages.

It puts your dependencies in the Nix store where they belong.

This tool requires Nix to be available, specifically the `nix-prefetch-url` and `nix-prefetch-git` tools.


## Nimble to Nix lockfile

```sh
cd «Nim project with a Nimble file»
nim_lk nimble-to-nix > lock.json
```
Generate a lockfile containing Nix [FOD](https://nixos.org/manual/nix/stable/glossary#gloss-fixed-output-derivation) store paths that can be converted to `nim.cfg` files.

See the local [lock.json](./lock.json) file as an example.

The following expression takes a path to a lockfile and generates a Nim configuration file that adds each entry in the lockfile to the module search path via the `path:` option.

```nix
{ pkgs ? import <nixpkgs> { }, lockPath, excludes ? [ ] }:

let
  inherit (pkgs) lib;
  buildPkgDir = depends:
    let
      fetchers = {
        fetchzip = { url, sha256, ... }:
          pkgs.fetchzip {
            name = "source";
            inherit url sha256;
          };
        git = { fetchSubmodules, leaveDotGit, rev, sha256, url, ... }:
          pkgs.fetchgit { inherit fetchSubmodules leaveDotGit rev sha256 url; };
      };
      srcDirs = map (attrs@{ method, srcDir, ... }:
        let fod = fetchers.${method} attrs;
        in if srcDir == "" then fod else "${fod}/${srcDir}") depends;
    in pkgs.runCommand "nim.cfg" {
      outputs = [ "out" "src" ];
      nativeBuildInputs = [ pkgs.xorg.lndir ];
      passthru = { inherit depends srcDirs; };
    } ''
      pkgDir=$src/pkg
      cat << EOF >> $out
      noNimblePath
      path:"$src"
      path:"$pkgDir"
      EOF
      mkdir -p "$pkgDir"
      ${lib.strings.concatMapStrings (d: ''
        lndir "${d}" "$pkgDir"
      '') srcDirs}
    '';
in with builtins;
lib.pipe lockPath [
  readFile
  fromJSON
  (getAttr "depends")
  (filter ({ packages, ... }: !(any (pkg: elem pkg excludes) packages)))
  buildPkgDir
]
```

I manage all this with [Tup](https://gittup.org/tup).

### Tuprules.tup
```
# Tuprules.tup

# A Tup macro to create lock.json.
!nim_lk = |> nim_lk | jq --compact-output --sort-keys > %o |> lock.json

# A Tup macro to build a symlink from the Nix store to nim.cfg.
!nim_cfg = |> nix build --file ./build-nim-cfg.nix --out-link %o --arg lockPath `pwd`/%f --arg excludes '[$(NIM_LOCK_EXCLUDES)]' |> nim.cfg

```

### Tupfile
```
# Tupfile
include_rules

# Omit the foobar library from nim.cfg.
NIM_LOCK_EXCLUDES += "foobar"

# Generate lock.json.
: |> !nim_lk |> | ./<lock>

# Generate nim.cfg.
: lock.json |> !nim_cfg |> | ./<lock>

# Build some Nim.
: foreach *.nim | ./<lock> |> nim c -o:%o %f |> %B
```


## Nimble to CycloneDX SBOM

```sh
cd «Nim project with a Nimble file»
nim_lk nimble-to-sbom > sbom.json
```

Generate a [CycloneDX](https://cyclonedx.org/) [Software Bill of Materials](https://cyclonedx.org/capabilities/sbom/) from a Nimble file.

A generated SBOM describes the package that corresponds to a given Nimble file and lists its direct and transitive dependencies.
The SBOM also contains a superset of the information in the Nix lockfile format, so the SBOM can also be used to generate Nim configuration files.

See the local [sbom.json](./sbom.json) file as an example.

Nix can extract FOD paths from SBOMS in a similar manner to lockfiles.
The [bom-to-nim-cfg.nix ](./bom-to-nim-cfg.nix) file demonstrates how to generate a Nim configuration that adds Nix store paths to the module lookup path.


## Nimble emulation

Nimble is an interpreter that executes Nim code in `*.nimble` scripts and when that script sets some variables in the right way then you have a package description.

This design is not even wrong as it allows packages to be described in arbitrary formats.

The [nimble file](./nim_lk.nimble) in this repository is a drop-in Nimble replacement for repositories that contain a `sbom.json` file.

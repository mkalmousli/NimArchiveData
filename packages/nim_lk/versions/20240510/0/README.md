# Nim lockfile generator

Generates Nix specific lock files for Nim packages.

It puts your dependencies in the Nix store where they belong.

```sh
cd «Nim project with a Nimble file»
nim_lk > lock.json
```

These lock files contain Nix [FOD](https://nixos.org/manual/nix/stable/glossary#gloss-fixed-output-derivation) store paths that can be converted to `nim.cfg` files.

```nix
{ pkgs ? import <nixpkgs> { }, lockPath, excludes ? [ ] }:

let inherit (pkgs) lib;
in with builtins;
lib.pipe lockPath [
  readFile
  fromJSON
  (getAttr "depends")
  (filter ({ packages, ... }: !(any (pkg: elem pkg excludes) packages)))
  (map ({ path, srcDir, ... }: ''path:"${storePath path}/${srcDir}"''))
  lib.strings.concatLines
  (pkgs.writeText "nim.cfg")
]
```

I manage all this with [Tup](https://gittup.org/tup).

```
# Tuprules.tup above my Nim projects
!nim_lk = |> nim_lk | jq --compact-output --sort-keys > %o |> lock.json

NIXEXPRS_DIR = $(TUP_CWD)/nixexprs
!nim_cfg = |> nix build --file $(NIXEXPRS_DIR)/configure.nix --out-link %o  --arg lockPath `pwd`/%f --arg excludes '[$(NIM_LOCK_EXCLUDES)]' |> nim.cfg
```

```
# Tupfile in a Nim project
include_rules
# Omit the foobar library from nim.cfg.
NIM_LOCK_EXCLUDES += "foobar"
: |> !nim_lk |> | ./<lock>
: lock.json |> !nim_cfg |> | ./<lock>
```

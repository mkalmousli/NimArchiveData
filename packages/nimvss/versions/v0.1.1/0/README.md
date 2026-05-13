# nimvss — Windows VSS for Nim

Minimal Nim library for working with Windows Volume Shadow Copy Service (VSS). It offers a pragmatic high‑level API to enumerate, create, and delete snapshots, while exposing a thin low‑level FFI when needed.

This library is designed to pair with [Nimewf](https://github.com/dropfra-me/nimewf) by [Nimager](https://github.com/dropfra-me/nimager) to enable VSS‑based EWF imaging on Windows.

## Architecture

* `src/nimvss.nim`: Umbrella module that re‑exports everything for `import nimvss`.
* `src/nimvss/snapshot.nim`: High‑level API to list/create/delete snapshots. Prefer using this.
* `src/nimvss/winvss.nim`: Minimal low‑level bindings for VSS COM interfaces (`vssapi.dll`).
* `src/nimvss/privs.nim`: Helpers to check/enable process token privileges (e.g., `SeBackupPrivilege`).

## Requirements

* Windows (VSS is a Windows technology)
* Nim `>= 2.0.0`, winim `>= 3.9.0` (see `nimvss.nimble`)
* Administrator shell is recommended; some actions require privileges

## Quickstart

Import the umbrella module to access both the high‑level API and helpers.

```nim
import nimvss
```

### Enumerate existing snapshots

Use VSS query context (`VSS_CTX_ALL`) to enumerate system snapshots.

```nim
import nimvss

var s = newVssSession(VSS_CTX_ALL)
let snaps = s.listSnapshots()
s.close()
for snap in snaps.values:
  echo snap.info  # multi‑line details
```

### Create a snapshot (e.g., for C:) and open a device handle

```nim
import nimvss

var s = newVssSession(persistent=false) # file‑share backup by default
let snap = s.createSnapshot("C:", openHandle=true)
s.close()

doAssert snap.state.handle != INVALID_HANDLE_VALUE
echo snap.info.devicePath  # \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopyXX
```

### Delete a snapshot by GUID

```nim
import nimvss

var s = newVssSession(VSS_CTX_ALL)
let snaps = s.listSnapshots()
let first = snaps.values.toSeq()[0]
discard s.deleteSnapshot(first.info.id)
s.close()
```

### Privileges (for future use)

Some operations benefit from `SeBackupPrivilege`.

```nim
import nimvss

# Check/enable privilege
if not privs.havePrivilege("SeBackupPrivilege"):
  discard privs.enablePrivilege("SeBackupPrivilege")
```

These examples mirror the usage in `tests/t_nimvss.nim`.

## API Surface (high‑level)

* `newVssSession*(persistent=false, withWriters=false): VssSession`
* `newVssSession*(ctx: VSS_SNAPSHOT_CONTEXT): VssSession`
* `close*(session)`
* `listSnapshots*(session): Table[GUID, Snapshot]`
* `createSnapshot*(session; volume: string; openHandle=false): Snapshot`
* `deleteSnapshot*(session; snapshotId: GUID|string; force=true): bool`
* `privs.enablePrivilege*(name, enable=true): bool`
* `privs.havePrivilege*(name): bool`

## Status and TODO

* [x] Stronger error handling and exceptions
* [ ] Expose/unexpose snapshots (drive letter or mount path)
* [ ] Writer support (gather metadata, prepare/commit, component selection)
* [ ] Raw device utilities (open existing device, sector size, length)
* [ ] More tests and examples

## Install & Build

* As a local dependency: add this repo to your workspace and `import nimvss` using `src` layout.
* Or `nimble develop` from the project root to make it available in your Nimble env.
* Run example/tests on Windows:
  * `nim c -r tests/t_nimvss.nim`

## License

MIT

<h1 align="center">winrm.nim</h1>

<p align="center">
  <b>Native WinRM client library for Nim</b>
</p>

<p align="center">
  <sub>
    <b>Version</b> 1.0.0 ·
    <b>Author</b> Chokri Hammedi (blue0x1) ·
    <b>License</b> MIT
  </sub>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/NTLM-08f5f9?style=for-the-badge" alt="NTLM">
  <img src="https://img.shields.io/badge/Kerberos-fc1383?style=for-the-badge" alt="Kerberos">
  <img src="https://img.shields.io/badge/PSRP-08f5f9?style=for-the-badge" alt="PSRP">
  <img src="https://img.shields.io/badge/WinRS-fc1383?style=for-the-badge" alt="WinRS">
  <img src="https://img.shields.io/badge/File%20Transfer-08f5f9?style=for-the-badge" alt="File Transfer">
  <img src="https://img.shields.io/badge/Zero%20Dependencies-fc1383?style=for-the-badge" alt="Zero Dependencies">
</p>

<p align="center">
  <a href="https://github.com/blue0x1/nim-winrm/wiki"><img src="https://img.shields.io/badge/Documentation-08f5f9?style=for-the-badge&logo=gitbook&logoColor=black" alt="Documentation"></a>
  <a href="https://github.com/blue0x1/nimrm"><img src="https://img.shields.io/badge/nimrm%20CLI-fc1383?style=for-the-badge&logo=windowsterminal&logoColor=white" alt="nimrm CLI"></a>
</p>

---

A complete, dependency-free implementation of the WinRM protocol stack including NTLM authentication, Kerberos via GSSAPI, PSRP (PowerShell Remoting Protocol), WinRS (Windows Remote Shell), NTLM message encryption, file transfers, and .NET assembly execution.

## Legal Notice

This library is intended for lawful administration, security testing, and research on systems you own or have explicit permission to access. The author is not responsible for misuse or damage caused by this software.

## Features

| Area | Details |
| --- | --- |
| Authentication | NTLMv2 password, NTLMv2 pass-the-hash, Kerberos via `libgssapi_krb5` |
| Transport | HTTP, HTTPS/TLS, NTLM message encryption (sealing) |
| PowerShell | PSRP session/runspace management, pipeline creation, output decoding |
| CMD | WinRS shell creation, command execution, binary output |
| Transfers | Chunked Base64 upload with adaptive retry, streamed download |
| In-Memory | PowerShell script import, managed .NET assembly detection and execution |
| Crypto | Native MD4, MD5, HMAC-MD5, RC4, NTLM key derivation (no OpenSSL for auth) |
| Protocol | SOAP/WS-Management envelope construction, SPNEGO/ASN.1 DER encoding |

## Requirements

| Component | Requirement |
| --- | --- |
| Nim | `>= 1.6.0` |
| Kerberos | `libgssapi_krb5.so.2` (Linux) or `libgssapi_krb5.dylib` (macOS) |
| TLS | OpenSSL (`-d:ssl`) |

## Installation

### Nimble

```bash
nimble install winrm
```

### Manual

Copy `winrm.nim` into your project and import it:

```nim
import winrm
```

## Quick Start

### NTLM Authentication

```nim
import winrm

var client = newClient(
  host   = "192.168.1.10",
  user   = "CORP\\administrator",
  pass   = "Password123",
  ntHash = "",
  spn    = "",
  domain = "",
  auth   = amNtlm,
  ssl    = false,
  port   = 5985
)

warmSmartShell(client)

let output = runCmd(client, "whoami", isCmd = false)
echo output

deleteShell(client)
```

### Pass-the-Hash

```nim
import winrm

var client = newClient(
  host   = "192.168.1.10",
  user   = "CORP\\administrator",
  pass   = "",
  ntHash = "aad3b435b51404eeaad3b435b51404ee:0123456789abcdef0123456789abcdef",
  spn    = "",
  domain = "",
  auth   = amNtlm,
  ssl    = false,
  port   = 5985
)

warmSmartShell(client)
echo runCmd(client, "hostname", isCmd = false)
deleteShell(client)
```

### Kerberos

```nim
import winrm

# Set KRB5CCNAME=FILE:/tmp/user.ccache before running

var client = newClient(
  host   = "dc01.corp.local",
  user   = "",
  pass   = "",
  ntHash = "",
  spn    = "",
  domain = "CORP.LOCAL",
  auth   = amKerberos,
  ssl    = false,
  port   = 5985
)

warmSmartShell(client)
echo runCmd(client, "Get-ADUser -Filter * | Select-Object Name", isCmd = false)
deleteShell(client)
```

### CMD Execution

```nim
warmSmartShell(client)
echo runCmdFast(client, "ipconfig /all", isCmd = true)
deleteShell(client)
```

### NTLM Encryption (Sealing)

```nim
var client = newClient(
  host   = "192.168.1.10",
  user   = "CORP\\administrator",
  pass   = "Password123",
  ntHash = "",
  spn    = "",
  domain = "",
  auth   = amNtlm,
  ssl    = false,
  port   = 5985,
  msgEnc = meAlways
)
```

## API Reference

### Types

```nim
AuthMethod* = enum amNtlm, amKerberos

MessageEncryption* = enum meAuto, meAlways, meNever

WinRMClient* = object
  host, username, password, ntHash, spn, domain: string
  auth: AuthMethod
  msgEnc: MessageEncryption
  useSSL: bool
  port: int
  shellId*: string
  remoteCwd*: string
  cmdShellDenied*: bool
  # ... internal fields

ChunkCallback* = proc(chunk: string)

PsrpDefragmenter* = object
```

### Error Types

```nim
WinRMError*               # Base error
WinRMAuthorizationError*  # Authentication failure
InvalidShellError*        # Shell no longer valid
WinRMWSManFault*          # WS-Management fault (faultCode, faultDescription)
WinRMSoapFault*           # SOAP fault (code, subcode, reason)
WinRMWMIError*            # WMI error (errorCode, error)
WinRMHTTPTransportError*  # HTTP transport error (statusCode)
```

### Client

| Proc | Description |
| --- | --- |
| `newClient*(host, user, pass, ntHash, spn, domain, auth, ssl, port, msgEnc): WinRMClient` | Create a new WinRM client |
| `warmSmartShell*(c)` | Initialize the optimal shell (PSRP or WinRS) |
| `ensureShell*(c, waitOpened)` | Ensure a PSRP shell/runspace is ready |
| `deleteShell*(c)` | Close and clean up the active shell |
| `closeNtlm*(c)` | Close the NTLM socket |
| `resetTransport*(c)` | Reset transport state for reconnection |

### Command Execution

| Proc | Description |
| --- | --- |
| `runCmd*(c, cmd, isCmd, mergeStreams): string` | Execute via PSRP with streaming output |
| `runCmdCollect*(c, cmd, isCmd, onChunk, mergeStreams): string` | Execute via PSRP and collect full output |
| `runCmdFast*(c, cmd, isCmd): string` | Execute via WinRS CMD shell |
| `runCmdFastCached*(c, cmd, isCmd, onChunk): string` | Execute via cached WinRS CMD shell |
| `runCmdFastOrPsrp*(c, cmd, isCmd): string` | Try WinRS first, fall back to PSRP |

### File Transfer

| Proc | Description |
| --- | --- |
| `uploadFileStream*(c, data, setup, total)` | Upload data in chunked Base64 fragments |
| `drawProgress*(label, current, total)` | Render a progress bar to terminal |

### Utilities

| Proc | Description |
| --- | --- |
| `genUuid*(): string` | Generate a random UUID |
| `encodePs*(cmd): string` | Base64-encode a PowerShell command (UTF-16LE) |
| `isManagedPe*(data): bool` | Check if binary data is a managed .NET PE |
| `isConnectionLostMessage*(msg): bool` | Check if error indicates connection loss |
| `psrpTextValue*(v): string` | Extract text from a PSRP value element |
| `psrpHexDecode*(text): string` | Decode PSRP hex-encoded Unicode text |
| `isRetryableFault*(faultCode): bool` | Check if a WS-Management fault is retryable |
| `secToDur*(seconds): string` | Format seconds as human-readable duration |

### PSRP

| Proc | Description |
| --- | --- |
| `newPsrpDefragmenter*(): PsrpDefragmenter` | Create a PSRP message defragmenter |
| `defragment*(d, base64Data): (complete, msgType, data)` | Feed a fragment and check for completion |
| `fragmentMessage*(objectId, msg, maxSize): seq[string]` | Fragment a PSRP message into Base64 chunks |

### Constants

```nim
RESOURCE_URI_CMD*        = "http://schemas.microsoft.com/wbem/wsman/1/windows/shell/cmd"
RESOURCE_URI_POWERSHELL* = "http://schemas.microsoft.com/powershell/Microsoft.PowerShell"
WSManMaxEnvelope*        = 153600
WSManOperationTimeout*   = 60
```

## Architecture

The library implements the full WinRM protocol stack in a single file:

- **NTLM**: NTLMv2 negotiate/challenge/authenticate with native MD4/MD5/HMAC-MD5/RC4
- **Kerberos**: GSSAPI FFI to system `libgssapi_krb5` with SPNEGO wrapping
- **SOAP/WS-Man**: XML envelope construction for shell lifecycle and command execution
- **PSRP**: PowerShell Remoting Protocol with session capability exchange, runspace management, pipeline creation, message fragmentation/defragmentation, and output decoding
- **WinRS**: Windows Remote Shell for CMD execution
- **NTLM Sealing**: Message encryption/decryption with signing for HTTP transport
- **Kerberos Wrapping**: GSS-API `gss_wrap`/`gss_unwrap` for message protection

## License

MIT. See [LICENSE](LICENSE).

# myip

Nim client for the MyIP (https://my-ip.io) API

## Installation

Run this command in your project directory:

```bash
nimble install myip
```

## Usage

### Import the package

```nim
import myip
```

### Get IP info (IPv4 and IPv6 supported)

```nim
# getIP(): IP
let ipInfo = getIP()
echo ipInfo
```

Sample output:

```json
{
  "success": true,
  "ip": "23.88.33.229",
  "type": "IPv4",
  "country": {
    "code": "DE",
    "name": "Germany"
  },
  "region": "Bavaria",
  "city": "Gunzenhausen",
  "location": {
    "lat": 49.1156,
    "lon": 10.7511
  },
  "timeZone": "Europe/Berlin",
  "asn": {
    "number": 24940,
    "name": "Hetzner Online GmbH",
    "network": "23.88.0.0/17"
  }
}
```

### Get IPv4 info

```nim
# getIPv4(): IP
let ipInfo = getIPv4()
echo ipInfo.ip
```

Sample output: `181.211.201.172`

### Get IPv6 info

```nim
# getIPv6(): IP
let ipInfo = getIPv6()
echo ipInfo.ip
```

Sample output: `3d0c:fe27:142e:f2fd:8c00:c1f8:c301:4dc0`

## Contributing

Contributions are welcome! Feel free to create an issue or open a pull request.

## License

This project is licensed under the terms of the [GNU GPL v3.0 License](https://www.gnu.org/licenses/gpl-3.0.html).

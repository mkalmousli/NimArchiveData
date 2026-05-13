# AWS
Rudimentary `aws-cli` wrapper.

> [!CAUTION]
> Currently only supports the EC2 command, with a small subset of subcommands

### Usage
```nim
import aws

echo describeInstances(filters = @[Filter(filterInstanceId, "i-0b1b3b3b3b3b3b3b3")]) # (NextToken: "", Reservations: @[])

echo runInstances(
    count = 42,
    disableApiStop = true,
    userData = "..."
)

echo getInstanceID() # get current instanceId
```

#### TODO
* Crawler to ingest all types and endpoints
* jsony_plus XML support
* -> AWS REST support over cli
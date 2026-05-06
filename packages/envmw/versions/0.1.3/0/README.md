# KV Service via local Environment Variables

It requires a key as path and any string as value, optional you can set an
expire time in seconds.

To get the value back, request the key-path starting `/get`.

## Examples

 - set: `curl -s -X POST http://127.0.0.1:6380 -d "key=/app/sub/key&value='{\"pi\":3.14}'&expire=3"`
 - set: `curl -s -X POST http://127.0.0.1:6380/set --header "Content-Type: application/json" -d '{"key":"/app/sub/key2", "value":"Hi", "expire":3}'`
 - get: `curl -s http://127.0.0.1:6380/get/app/sub/part/key`

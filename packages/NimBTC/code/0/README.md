- A BTC wrapper for nim

- Note: This is not fully tested, as some of the endpoints are complex and time consuming to test. The **Bitcoin Core's RPC documentation is sloppy and sometimes out of date, the defaults they specify has been wrong on several occasions during testing.** For example, the default for decriptors is wrong on the createWallet endpoint. This was corrected in the wrapper.

Example Usage:

```nim
import NimBTC
import Json
import pretty
let client = initBTCClient("http://127.0.0.1:18332/", "user", "7wFULRLmjxXSH1kqUSB2NLR3MrsYCc8l0GWP_6nccug")
echo createWallet(client, "user")
echo loadWallet(client, "user")

let blockTest = getBestBlockHash(client).resultObject.getStr()
let transactions = getblock(client, blockTest, HighVerbosity).resultObject["tx"]
let txid = transactions[5]["txid"].getStr()
echo txid
let txRawBase = getrawtransaction(client, txid, true)
let originBlock = txRawBase.resultObject["vin"][0]["txid"].getStr()
let txRawMinusOne = getrawtransaction(client, originBlock, true)
print txRawBase
print txRawMinusOne
```

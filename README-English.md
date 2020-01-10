[README | 中文 ](https://github.com/ofproject/ofwallet/blob/master/README.md)
[README | English](https://github.com/ofproject/ofwallet/blob/master/README-English.md)

# Ofbank wallet interface description
 
## 1、Summary
### 1.1 technical specifications
The unified external interface is developed based on HTTP1.1 protocol, with unlimited transmission mode and UTF-8 character set

### 1.2 operation flow
1. Synchronize block information when starting operation
2. User registration to obtain wallet address
3. Transfer by wallet address and query balance
4. Query block information and transaction records through the interface

## 2、Operation procedure
### 2.1 Running Wallet
When ofbank runs for wallet, it starts regret synchronization block information, which is fast synchronization by default

**command：**
```
nohup ./ofbank --maxpeers 100 --cache 128 --datadir  dataOF  --rpc --rpcaddr 127.0.0.1 --rpcport 8888 --port 30303 --rpcapi ofbank > data.log 2>&1 &
```

Default value：
```
--rpcport 8888 --port 30303  --rpcaddr 127.0.0.1
```

Note: the Windows version cannot be opened and run directly, but it also needs to be started with the command


## 3、 Interface description
The ofbank wallet interface is used to interact with local or remote of nodes, use HTTP to establish a link with of nodes, submit JSON format parameters to of chain through post method, and return corresponding JSON format results


### 3.1 Interface instruction

| JS instructions | Instruction specification |
| --- | --- |
| ofbank.register | Generate wallet address (including private key file) |
| ofbank.unlockAccount | Unlock Wallet |
| ofbank.sendTrans | Transfer accounts |
| ofbank.checkTrans | Transaction query |
| ofbank.getBalance | Balance inquiry |
| ofbank.getBlock | Block information query |
| ofbank.blockHeight | Block height query |
| ofbank.export | Export private key |
| ofbank.import | Import private key |
| ofbank.getAddrSendCount |  Get the nonce value of the account |
| ofbank.sendRawTrans | Send signed transaction |


### 3.2 Parameter description
| parameter | type | description |
| --- | --- | --- |
| jsonrpc | string | RPC current version No. [optional] |
| id | string | Unique identifier of the current sending event [required] |
| method | string | Event method [required] |
| params | array | Method parameter [required] |
| result | string | Method return value |
| code | string | error code |


### 3.3 Error code table
| number | explain e
| --- | --- |
| -32000 | Logic error |
| -32600 | Invalid request | 
| -32601 | This method is not provided | 
| -32602 | Invalid parameter | 
| -32700 | Invalid received information |


### 3.3 Data rule
**3.3.1 register**
Method：ofbank_register
```
parameter：
Password (string) [compulsory]
Return value: address hash value (string)
Request example：
{
    "jsonrpc":"3.0",
    "id":"2", 
    "method":"ofbank_register", 
    "params":["123..123"] 
}
Response success：
{
    "jsonrpc": "3.0",
    "id": "2",
    "result": "0x00000a009cba83635a98ce95b9b755977d3c5b13c8424e790f"
}

```
    
    
**3.3.2 Unlock**
method：ofbank_unlockAccount
```
parameter：
Account address to be unlocked (string) 【required】
Account password to be unlocked (string) 【required】
return value：true/false (bool)
Request example：
{
    "jsonrpc": "3.0",
    "id":"2", 
    "method":"ofbank_unlockAccount", 
    "params":                               ["0x00000a009ca2640667daffa938184f5e0d9c6526054f0d0e66","123..123"]
}
Response succeeded：
{
"jsonrpc": "3.0",
"id": "2",
"result": true
}	
```

**3.3.3 Transfer**
method：ofbank_sendTrans
```
Account needs to be unlocked before transfer, unlock reference3.3.2
parameter：
Transfer origination address (from) (string) 【required】
Transfer acceptance address (to) (string) 【required】
transfer amount (value) (string) 【required】
Transaction gas (gas) (string)【required】(0x5208)
gas price (gasPrice) (string)【required】(0x48c27395000)
return value：Transaction hash value (string)
Request example：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_sendTrans", 
    "params":   [{"from":"0x00000a009ca2640667daffa938184f5e0d9c6526054f0d0e66","to":"0x00000a009c45ecdccb0b6f01fb7505f6ff5d5df32119eeca6e","value":"100000","gas":"0x5208","gasPrice":"0x48c27395000"}]
}
Response succeeded：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result":   "0xc45788188eb6f087c3e48d3fd28b636edb0828981f3428533dafe1b057131fe5"
}
```

**3.3.4 Transaction inquiry**
 ```
	method：ofbank_checkTrans
	parameter：Transaction hash value (string) 【required】
	Return value: transaction details(string)
	Request example：
	{
		"jsonrpc": "3.0",
		"id":"3", 
		"method":"ofbank_checkTrans", 
		"params":		["0xc45788188eb6f087c3e48d3fd28b636edb0828981f3428533dafe1b057131fe5"]
	}
	Response succeeded：
	{
   		"jsonrpc": "3.0",
   		"id": "3",
 		"result": {
        		"blockHash": "0xdd7ff27082599b7f3470814879b1ab5d0d92fe1ad2202de1da605279cf35579d",
        		"blockNumber": "0x80f",
        		"from": "0x00000a009ca2640667daffa938184f5e0d9c6526054f0d0e66",
        		"gas": "0xb444",
        		"gasPrice": "0xb4",
        		"txFee": "0.000",
        		"timestamp": "1537345799",
        		"hash": "0xc45788188eb6f087c3e48d3fd28b636edb0828981f3428533dafe1b057131fe5",
        		"input": "0x",
        		"nonce": "0x253",
        		"to": "0x00000a009c45ecdccb0b6f01fb7505f6ff5d5df32119eeca6e",
        		"useTime": "0x0",
        		"transactionIndex": "0x2",
        		"value": "100000.000",
        		"v": "0x1c",
        		"r": "0x49758ef129d1fccd83269ecae062984072f7f5352bfc2a3b0b17fefa746a7370",
        		"s": "0x11f71bbffa0ec59a43b481f472e79b5f65eaca2da892709580e38f0edcb312e4"
  	 	 }
	}
```

**3.3.5 Balance inquiry**
method：ofbank_getBalance
```
parameter：
Wallet address hash value (string)【required】
Block height to be queried (string) 【required】(Hex, "latest" means the last piece)
Return value: address balance (string)
Request example：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_getBalance", 
    "params":	    ["0x00000a009ca2640667daffa938184f5e0d9c6526054f0d0e66","latest"]
}
Response succeeded：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result": "702488.407759"
}
```

**3.3.6 Block height query**
```
method：ofbank_blockHeight
parameter：null
Return value: Total number of blocks (string) (Hexadecimal)	
Request example：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_blockHeight"
}
Response succeeded：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result": "0x42"
}
```

**3.3.7 Block information query**
method：ofbank_getBlock 
```
parameter：
Number of blocks to query(int64)【required】
Return value: query block information (string)
Request example：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_getBlock", 
    "params":[1]
}
Response succeeded：
{
    ”jsonrpc": "3.0",
    "id": "3",
    "result": {
    "coinbase": "0x00000a009ca2640667daffa938184f5e0d9c6526054f0d0e66",
    "difficulty": "0x20000",
    "extraData":    "0xd983020004846765746888676f312e31302e338664617277696e",
    "gasLimit": 5247999,
    "gasUsed": "0x0",
    "hash":     "0xe6aa67f75e7621bc42dd734e1b0e9d12316e656ace396dc9df0a54028ee6d411,
"miner": "0x00000a009ca2640667daffa938184f5e0d9c6526054f0d0e66",
    "mixHash": "0x768170aa3a66101a761d6482818b6b0422e2f0e97083e084a2a30ff90576feea,
    "nonce": "0x0301c29b03cfc9d9",
    "number": "0x1",
    "parentHash": "0xdd965dd34ef9c6b7f104bf5f1de9718bcb126502766fb1fca5f45babd8bb7320,
    "pos": 	[{
     "minerbase":   "0x00000a009ca2640667daffa938184f5e0d9c6526054f0d0e66",
    "percentage": "100%"
            }],
    "receiptsRoot":     "0x000100020003000400050006000700080009000a000b000c000d000e000f0000,
    "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347,
"size": "0x25a",
"stateRoot": "0x9b019cc3a98aa052c35ca26aa9f746284b0ec3549e09cd61629a7c767de4c428,
"timestamp": "0x5b9cb0b6",
"totalDifficulty": "0xa10a",
"transactions": [],
"transactionsRoot": "0x000100020003000400050006000700080009000a000b000c000d000e000f0000",
"uncles": []
}
}
```

**3.3.8 Export private key**
method：ofbank_export
```
parameter：
Wallet address hash value (string)【required】
password (string)【required】
Return value: Private key(string)
Request example：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_export", 
    "params":["0x000017009c5ead20c953687905905ce0681486c7f0e2bc7f84","123456"]
}
Response succeeded：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result": "3990f973a98cd88bb7e5072906c17f57d84a6e13ae919bc3384fbd6acdab8626"
}
```

**3.3.9 Import private key**
method：ofbank_import
```
parameter：
password (string)【required】
Wallet private key (string)【required】
Wallet address (string)【required】
return：null(The private key storage directory generates a new private key file)
Request example：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_import", 
    "params":["123456","3990f973a98cd88bb7e5072906c17f57d84a6e13ae919bc3384fbd6acdab8626","0x000017009c5ead20c953687905905ce0681486c7f0e2bc7f84"]
}
Response succeeded：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result": null
}
```

**3.3.10 Get account nonce value**
method：ofbank_getAddrSendCount
```
parameter：
Wallet address hash value (string)【required】
Block status (string)【required】(Hexadecimal，“pending”)
Return value: the nonce value of the transaction sent by the current address (string) (Hexadecimal)	
Request example：
{
    "jsonrpc": "3.0",
    "id":"3", 
    “method":"ofbank_getAddrSendCount"
    "params":["0x00000a009c34c2f0bba306ea2bc7333eccec42d93b16758418","pending"]
}
Response succeeded：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result": "0x27"
}
```


**3.11  Post signed transaction** 
method：ofbank_sendRawTrans	
```
parameter：
Signing transaction data (string) 【required】(Hexadecimal)				
Return value: transaction hash value (string)
Request example：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_sendRawTrans", 
    "params":	["0x00000017009c5ead20c017009c5ead20c953687905905ce0681486c7f0e2bc7f8400017009c5ead20c"]
}
Response succeeded：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result":  "3990f973a98cd88bb7e5072906c17f57d84a6e13ae919bc3384fbd6acdab8626"
}
```

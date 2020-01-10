[README | English](https://github.com/ofproject/ofwallet/blob/master/README-English.md)

[README | 中文 ](https://github.com/ofproject/ofwallet/blob/master/README.md)

# OFBANK钱包接口说明


## 一、 概述
### 1.1 技术规格
统一提供的对外接口，基于http1.1协议开发，传输方式不受限，字符集为UTF-8

### 1.2 业务流程
1. 启动运行时，同步区块信息
2. 用户注册，获取钱包地址
3. 利用钱包地址进行转账，查询余额操作
4. 通过接口查询区块信息和交易记录

## 二、运行时启动

### 2.1 运行钱包
ofbank为钱包运行时，启动后会同步区块信息，默认快速同步
**命令：**
```
nohup ./ofbank --maxpeers 100 --cache 128 --datadir  dataOF  --rpc --rpcaddr 127.0.0.1 --rpcport 8888 --port 30303 --rpcapi ofbank > data.log 2>&1 &
```

默认值：
```
--rpcport 8888 --port 30303  --rpcaddr 127.0.0.1
```

注意：windows版不能直接打开运行，同样也要使用命令启动


## 三、接口说明
OFBank钱包接口用于和本地或者远程OF节点进行交互，使用HTTP建立与OF节点的链接，通过POST方法将JSON格式的参数提交到OF链上，并返回相应的JSON格式结果


### 3.1 接口指令

| JS指令 | 指令介绍 |
| --- | --- |
| ofbank.register | 生成钱包地址（包括私钥文件) |
| ofbank.unlockAccount | 解锁钱包 |
| ofbank.sendTrans | 转账 |
| ofbank.checkTrans | 交易查询 |
| ofbank.getBalance | 余额查询 |
| ofbank.getBlock | 区块信息查询 |
| ofbank.blockHeight | 区块高度查询 |
| ofbank.export | 导出私钥 |
| ofbank.import | 导入私钥 |
| ofbank.getAddrSendCount |  获取账户nonce值 |
| ofbank.sendRawTrans | 发送签名交易 |


### 3.2 参数说明
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| jsonrpc | 字符串 | RPC当前版本号【选填】 |
| id | 字符串 | 当前发送事件唯一标识符，与响应结果对应【必填】 |
| method | 字符串 | 事件方法【必填】 |
| params | 数组 | 方法参数【必填】 |
| result | 字符串 | 方法返回值 |
| code | 字符串 | 错误代码 |


### 3.3 错误代码表
| 错误代码 | 说明 |
| --- | --- |
| -32000 | 逻辑错误 |
| -32600 | 无效的请求 | 
| -32601 | 没有提供此方法 | 
| -32602 | 无效的参数 | 
| -32700 | 接受信息无效|


### 3.3 数据规则
**3.3.1 注册**
方法：ofbank_register
```
参数：
密码 (string)【必填】
返回值：地址hash值 (string)
请求示例：
{
    "jsonrpc":"3.0",
    "id":"2", 
    "method":"ofbank_register", 
    "params":["123..123"] 
}
响应成功：
{
    "jsonrpc": "3.0",
    "id": "2",
    "result": "0x00000a009cba83635a98ce95b9b755977d3c5b13c8424e790f"
}

```
    
    
**3.3.2 解锁**
方法：ofbank_unlockAccount
```
参数：
需解锁的账户地址 (string) 【必填】
需解锁的账户密码 (string) 【必填】
返回值：true/false (bool)
请求示例：
{
    "jsonrpc": "3.0",
    "id":"2", 
    "method":"ofbank_unlockAccount", 
    "params":                               ["0x00000a009ca2640667daffa938184f5e0d9c6526054f0d0e66","123..123"]
}
响应成功：
{
"jsonrpc": "3.0",
"id": "2",
"result": true
}	
```

**3.3.3 转账**
方法：ofbank_sendTrans
```
转账前需解锁账户，解锁参照3.3.2
参数：
转账发起地址 (from) (string) 【必填】
转账接受地址 (to) (string) 【必填】
转账金额 (value) (string) 【必填】
交易的gas (gas) (string)【必填】(0x5208)
gas的价格 (gasPrice) (string)【必填】(0x48c27395000)
返回值：交易hash值 (string)
请求示例：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_sendTrans", 
    "params":   [{"from":"0x00000a009ca2640667daffa938184f5e0d9c6526054f0d0e66","to":"0x00000a009c45ecdccb0b6f01fb7505f6ff5d5df32119eeca6e","value":"100000","gas":"0x5208","gasPrice":"0x48c27395000"}]
}
响应成功：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result":   "0xc45788188eb6f087c3e48d3fd28b636edb0828981f3428533dafe1b057131fe5"
}
```

**3.3.4 交易查询**
 ```
	方法：ofbank_checkTrans
	参数：交易hash值 (string) 【必填】
	返回值：交易的详细信息 (string)
	请求示例：
	{
		"jsonrpc": "3.0",
		"id":"3", 
		"method":"ofbank_checkTrans", 
		"params":		["0xc45788188eb6f087c3e48d3fd28b636edb0828981f3428533dafe1b057131fe5"]
	}
	响应成功：
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

**3.3.5 余额查询**
方法：ofbank_getBalance
```
参数：
钱包地址hash值 (string)【必填】
需查询的区块高度 (string) 【必填】(16进制，“latest”表示最后一块)
返回值：地址余额 (string)
请求示例：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_getBalance", 
    "params":	    ["0x00000a009ca2640667daffa938184f5e0d9c6526054f0d0e66","latest"]
}
响应成功：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result": "702488.407759"
}
```

**3.3.6 区块高度查询**
```
方法：ofbank_blockHeight
参数：无
返回值：区块总数 (string) (16进制)	
请求示例：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_blockHeight"
}
响应成功：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result": "0x42"
}
```

**3.3.7 区块信息查询**
方法：ofbank_getBlock 
```
参数：
需查询的区块数 (int64)【必填】
返回值：查询的区块信息 (string)
请求示例：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_getBlock", 
    "params":[1]
}
响应成功：
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

**3.3.8 导出私钥**
方法：ofbank_export
```
参数：
钱包地址hash值 (string)【必填】
密码 (string)【必填】
返回值：私钥(string)
请求示例：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_export", 
    "params":["0x000017009c5ead20c953687905905ce0681486c7f0e2bc7f84","123456"]
}
响应成功：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result": "3990f973a98cd88bb7e5072906c17f57d84a6e13ae919bc3384fbd6acdab8626"
}
```

**3.3.9 导入私钥**
方法：ofbank_import
```
参数：
密码 (string)【必填】
钱包私钥 (string)【必填】
钱包地址 (string)【必填】
返回值：null(私钥存放目录生成新的私钥文件)
请求示例：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_import", 
    "params":["123456","3990f973a98cd88bb7e5072906c17f57d84a6e13ae919bc3384fbd6acdab8626","0x000017009c5ead20c953687905905ce0681486c7f0e2bc7f84"]
}
响应成功：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result": null
}
```

**3.3.10 获取账户nonce值**
方法：ofbank_getAddrSendCount
```
参数：
钱包地址hash值 (string)【必填】
区块状态 (string)【必填】(16进制，“pending”)
返回值：当前地址已经发送交易的nonce值 (string) (16进制)	
请求示例：
{
    "jsonrpc": "3.0",
    "id":"3", 
    “method":"ofbank_getAddrSendCount"
    "params":["0x00000a009c34c2f0bba306ea2bc7333eccec42d93b16758418","pending"]
}
响应成功：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result": "0x27"
}
```


**3.11  发送签名后交易** 
方法：ofbank_sendRawTrans	
```
参数：
签名交易数据 (string) 【必填】(16进制)				
返回值：交易hash值 (string)
请求示例：
{
    "jsonrpc": "3.0",
    "id":"3", 
    "method":"ofbank_sendRawTrans", 
    "params":	["0x00000017009c5ead20c017009c5ead20c953687905905ce0681486c7f0e2bc7f8400017009c5ead20c"]
}
响应成功：
{
    "jsonrpc": "3.0",
    "id": "3",
    "result":  "3990f973a98cd88bb7e5072906c17f57d84a6e13ae919bc3384fbd6acdab8626"
}
```

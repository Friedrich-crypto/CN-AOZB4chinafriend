# AOZB配置轻节点指南
谢谢你们中国的很好朋友们很多支持对项目的！来自德国的问候作者，我是来自Genes链的炸富裕，很高兴结识你们！
## AOZB参数

AOZB：5210万枚，Decimal：8（小数点8位）
当前超级节点挖矿难度手续费：0.2%,不足5按5计

## 基础概念

AOZB链端采用符合ECC标准的公私钥地址体系，一个唯一的账户具有以下几个要素：用户私钥(usrPrivateKey)，标准私钥(stdPrivateKey)，公钥(public key)，地址(address)。其中，用户私钥是生成钱包时输入的随机字符串，每一个字符串唯一对应着一个地址，并可以根据ECC加密原理导出该地址的长私钥，因此，用户私钥应该取的足够长并保证不可能重复，并且记录于冷钱包内。标准私钥是根据用户私钥计算出的符合标准格式的私钥，发送交易时应该用私钥进行签名。公钥是动态生成的，随签名一并发送以验证用户身份有效性。地址是用户账号标识，由0x+23位十六进制数字组成。
## 名词解释
usrPrivateKey：用户私钥，即seed
stdPrivateKey：最标准私钥，通常用于导入到其他钱包、转账、签名等主要流程
address：钱包地址
hash：即uuid，每笔Tx的唯一标识
asset：即TOKEN名，即“AOZB”，大小写严格匹配



## 配置文件说明

配置文件在根目录下'config.ini'中

> [chain]
>
> full_node     =     http://154.223.40.9:5007 #上行全节点地址
>
> chain_type    =     genesChain
>
> chain_version   =       1.4.5
>
> network         =        main  
>
> [node]
>
> port         =     5006 #本地RPC端口
>
> rpc               =     HTTP
>
> inv_time        =       10  #节点进行同步时区块间隔时间，单位为秒
>
> sync_limit        =       100  #节点同步时单次请求块数上线，越大对网络要求越高
>
> log             =       node.log  #日志文件输出位置

## json-RPC

json-RPC基于HTTP协议，所有参数均为数组传入

错误：

- ChainError：全节点宕机
- AccountError：私钥有误
- DataError：数据格式错误



**method: chain.version**

- params: []

- 用途: 获取当前引擎版本

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"chain.version", "params":[]}'  127.0.0.1:5006

- example output:

  > {"id":"0","jsonrpc":"2.0","result":"seaQlord +U 1.4.5"}    

**method: chain.blocknumber**

- params: []

- 用途: 返回当前区块高度，full-node为全节点上高度，node为当前同步高度

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"chain.blocknumber", "params":[]}'  127.0.0.1:5006

- example output:

  > {"id":"0","jsonrpc":"2.0","result":121}

**method: chain.blocks**

- params: [start:int, end:int or 'latest', raw:bool 默认False]

- 用途: 获取[start,end)的区块内容，raw是是否显示原始数据

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"chain.blocks", "params":[0,1]}'  127.0.0.1:5006

- example output:

  > {"id":"0","jsonrpc":"2.0","result":{"blocks":[{"mining_time":1564661332,"txs":["f8a388b07e6111e9a9799cb6d0e78637","256e76ca7e6211e9a9799cb6d0e78637","2881fd507e6211e9a9799cb6d0e78637","2b1f0d8c7e6211e9a9799cb6d0e78637","2f0c9a547e6211e9a9799cb6d0e78637"]}],"end":1,"start":0}}


**method: chain.balance**

- params: [address: str, asset: str 可选]

- 用途: 获取address账户asset余额，若asset不填返回全部

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"chain.balance", "params":["0x1de1c511e0a885ddf4872eb"]}'  127.0.0.1:5006

- example output:

  > {"id":"0","jsonrpc":"2.0","result":{"address":"0x1de1c511e0a885ddf4872eb","balance":{"AOZB":5204846199816192}}}

**method: chain.storage**

- params: [asset: str, storage: str 可选]

- 用途: 获取asset存储空间下storage的数值，若storage不填返回全部，storage不存在时返回''

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"chain.storage", "params":["AOZB"]}'  127.0.0.1:5006

- example output:

  > {"id":"0","jsonrpc":"2.0","result":{"asset":"AOZB","storage":[{"key":"base","value":"50000000000000000"},{"key":"fee","value":"0.0002"},{"key":"interestType","value":"day"},{"key":"lastCallTime","value":"1565018636"},{"key":"owner","value":"0x1de1c511e0a885ddf4872eb"},{"key":"rangedRate","value":"[{\"rate\":0.001,\"base_upper_limit\":58075200.43315333,\"base_lower_limit\":39246455.453581095},{\"rate\":-79644879.2053973,\"base_upper_limit\":-63811143.68418092,\"base_lower_limit\":65209798.23441714},{\"rate\":44273665.041297674,\"base_upper_limit\":-23240981.812332645,\"base_lower_limit\":-69903194.81368083},{\"rate\":73861433.09620064,\"base_upper_limit\":71077083.44844371,\"base_lower_limit\":64836720.14730969},{\"rate\":-28274440.234293237,\"base_upper_limit\":-47771025.62550812,\"base_lower_limit\":41666018.409173906}]"}]}}






**method: chain.asset**

- params: [asset: str, showBalance: bool默认False]

- 用途: 获取asset详情，总发行数和持有人列表,若打开showBalance则出现'balance'数组记录各持有人的持有数

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"chain.asset", "params":["AOZB"]}'  127.0.0.1:5006

- example output:

  > {"id":"0","jsonrpc":"2.0","result":{"asset":"AOZB","holders":["0x1de1c511e0a885ddf4872eb","0x02ed107fc7e657f6539f26d","0xe713feb8a17d5cad19fafec","0x26b1e5a3d5b868f3f3cd819","0x0333ba1c7f0be3f617d52cd","0x5d9a4373e5551fcc09f526a","0xa3ec1e34cd2b27351e989d0"],"totalSupply":5210000000000000}}



**method: tx.byhash**

- params: [uuid: str]

- 用途: 返回uuid的tx详情

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"tx.byhash", "params":["f8a388b07e6111e9a9799cb6d0e78637"]}'  127.0.0.1:5006

- example output:

  > {"id":"0","jsonrpc":"2.0","result":{"block":0,"status":"success","tx":{"amount":10000,"asset":"AOZB","from":"0x00000000000000000000000","method":"transfer","mining_time":1564661332,"params":"","time":1564661331,"to":"0x64da5cbee5caaf943321998","type":0,"uuid":"f8a388b07e6111e9a9799cb6d0e78637"}}}


**method: tx.filter**

- params: [start:int, end:int or 'latest', filter: dict]

- 用途: 返回[start,end)的区块中符合filter过滤的内容，filter格式为[{"key":str,"value":str}]

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"tx.filter", "params":[0,1,[{"key":"asset","value":"AOZB"}]]}'  127.0.0.1:5006

- example output:

  > {"id":"0","jsonrpc":"2.0","result":{"end":1,"start":0,"txs":[{"amount":10000,"asset":"AOZB","from":"0x00000000000000000000000","method":"transfer","mining_time":1564661332,"params":"","time":1564661331,"to":"0x64da5cbee5caaf943321998","type":0,"uuid":"f8a388b07e6111e9a9799cb6d0e78637"},{"amount":1000,"asset":"AOZB","from":"0x64da5cbee5caaf943321998","method":"transfer","mining_time":1564661332,"params":"","time":1564661331,"to":"0xd48255723fe25276119e47d","type":0,"uuid":"256e76ca7e6211e9a9799cb6d0e78637"},{"amount":100,"asset":"AOZB","from":"0x64da5cbee5caaf943321998","method":"transfer","mining_time":1564661332,"params":"","time":1564661331,"to":"0x0d4b6d3fc179fae36fb7a70","type":0,"uuid":"2881fd507e6211e9a9799cb6d0e78637"},{"amount":100,"asset":"AOZB","from":"0x64da5cbee5caaf943321998","method":"transfer","mining_time":1564661332,"params":"","time":1564661331,"to":"0x231065a143321fc5da39ca7","type":0,"uuid":"2b1f0d8c7e6211e9a9799cb6d0e78637"},{"amount":100,"asset":"AOZB","from":"0xd48255723fe25276119e47d","method":"transfer","mining_time":1564661332,"params":"","time":1564661331,"to":"0x0d4b6d3fc179fae36fb7a70","type":0,"uuid":"2f0c9a547e6211e9a9799cb6d0e78637"}]}}


**method: tx.send**

- params: [tx, stdPvt]

- 用途: 发送tx，tx中可不含uuid，uuid会自动填充，from要和stdPvt对应

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"tx.filter", "params":[{"from":"0x59110487a1708e6ab267e63","to":"0x0333ba1c7f0be3f617d52cd","asset":"AOZB","amount":100},"0x2741a4faf54934cbcc57eacf92e18a1eb97b299614334a982d124fba6ccc411cc"]}'  127.0.0.1:5006



**method: watch.add**

- params: [filter: dict]

- 用途: 添加一个监听filter，filter格式为[{"key":str,"value":str}]， 返回filter的watchid

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"watch.add", "params":[[{"key":"to","value":"0x59110487a1708e6ab267e63"}]]}'  127.0.0.1:5006

- example output:

  > {"id":"0","jsonrpc":"2.0","result":1}

**method: watch.list**

- params: []

- 用途: 列出目前监听的所有filter

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"watch.list", "params":[]}'  127.0.0.1:5006

- example output:

  > {"id":"0","jsonrpc":"2.0","result":[{"data":[{"key":"from","value":"0x59110487a1708e6ab267e63"}],"id":0}]}



**method: watch.edit**

- params: [id, filter]

- 用途: 修改对应id的filter

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"watch.edit", "params":[0, [{"key":"to","value":"0x59110487a1708e6ab267e63"}]]}'  127.0.0.1:5006


**method: watch.del**

- params: [id]

- 用途: 将对应id的filter置为[]

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"watch.del", "params":[0]}'  127.0.0.1:5006

**method: watch.index**

- params: []

- 用途: 返回目前监听事件计数器

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"watch.index", "params":[]}'  127.0.0.1:5006
- example output:

  > {"id":"0","jsonrpc":"2.0","result":1}


**method: watch.pull**

- params: [offset:int 默认0, limit:int 可不填]

- 用途: 返回监听事件

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"watch.pull", "params":[]}'  127.0.0.1:5006
- example output:

  >{"id":"0","jsonrpc":"2.0","result":[{"data":{"amount":100,"asset":"AOZB","from":"0x59110487a1708e6ab267e63","method":"transfer","mining_time":1565179615,"params":"","time":1565179614,"to":"0x0333ba1c7f0be3f617d52cd","type":0,"uuid":"d9028cb0b90b11e98d3900163e029590"},"id":0}]}


**method: crypto.fromUsrPvt**

- params: [usrPvt]

- 用途: 将用户私钥转换为对应信息

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"crypto.fromUsrPvt", "params":["test1"]}'  127.0.0.1:5006
- example output:

  >{"id":"0","jsonrpc":"2.0","result":{"address":"0x59110487a1708e6ab267e63","publickKey":"0x8e90f9829f52cf70f4636eb9c2c508682fa869e2f9bf7f8dc1582c6bxbelewx0x10b345a201e327d863a2a82e03fb6be9583f26822ea0e6e582704e68","std_privateKey":"0x2741a4faf54934cbcc57eacf92e18a1eb97b299614334a982d124fba6ccc411cc","usr_privateKey":"test1"}}

**method: address.new**

- params: []

- 用途: 同crypto.fromUsrPvt，不过为随机生成

**method: crypto.fromStdPvt**

- params: [stdPvt]

- 用途: 将std私钥转换为对应信息

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"crypto.fromStdPvt", "params":["0x2741a4faf54934cbcc57eacf92e18a1eb97b299614334a982d124fba6ccc411cc"]}'  127.0.0.1:5006
- example output:

  >{"id":"0","jsonrpc":"2.0","result":{"address":"0x59110487a1708e6ab267e63","publickKey":"0x8e90f9829f52cf70f4636eb9c2c508682fa869e2f9bf7f8dc1582c6bxbelewx0x10b345a201e327d863a2a82e03fb6be9583f26822ea0e6e582704e68","std_privateKey":"0x2741a4faf54934cbcc57eacf92e18a1eb97b299614334a982d124fba6ccc411cc"}}


**method: crypto.signByStdPvt**

- params: [msg:str ,stdPvt]

- 用途: 手动签名

- example input:

  > curl -i -X POST    -H "Content-Type: application/json"    -d '{"jsonrpc":"2.0","id":"0","method":"crypto.signByStdPvt", "params":["test","0x2741a4faf54934cbcc57eacf92e18a1eb97b299614334a982d124fba6ccc411cc"]}'  127.0.0.1:5006
- example output:

  >{"id":"0","jsonrpc":"2.0","result":{"sgn":"0x51a82715c20d85d682d6f801926b43f83f2db638e57fd36423e7aad1xbelewx0x283ad0c41a611174ca464eadd3ee343828ef79b391044b996319ca1e","std_privateKey":"0x2741a4faf54934cbcc57eacf92e18a1eb97b299614334a982d124fba6ccc411cc"}}




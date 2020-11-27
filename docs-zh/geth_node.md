
# 共识节点加入

共识节点加入可以采用两种方式：一种是连接bootnode的enode接口，另一种是连接其中一个共识节点的enode接口。

## 连接bootnode的enode接口

### 安装geth

[安装geth和部署验证节点教程](./geth.md)

### 添加节点

开启一个新终端窗口，运行下面命令查看`node1`的 `enode` 字符串

```shell script
geth --exec 'admin.nodeInfo.enode' attach node1/geth.ipc
"enode://241a5bfdc880636ae7bc9da6292a6b1f8db81e1c87df2d81a9eef8f852aa260e669c2543a6584e34981dbb0d2c7b06edcb5334135b71a4ee00efdc38bf2e7699@127.0.0.1:26681"
```

创建`node4`

```shell script
mkdir node4

echo '12341234' > node4/password.txt

geth --datadir node4/ account new --password node4/password.txt

geth --datadir node4/ init genesis.json
```

```shell script
Your new key was generated

Public address of the key:   0xFE35176AE93c44C32DE90468fb51Cf1C6AAf0ed8
Path of the secret key file: node4/keystore/UTC--2020-11-24T09-39-42.399924729Z--fe35176ae93c44c32de90468fb51cf1c6aaf0ed8

- You can share your public address with anyone. Others need it to interact with you.
- You must NEVER share the secret key with anyone! The key controls access to your funds!
- You must BACKUP your key file! Without the key, it's impossible to access account funds!
- You must REMEMBER your password! Without the password, it's impossible to decrypt the key!
```

### 启动节点

```shell script
geth --datadir node4/ --syncmode 'full' --gcmode=archive --port 26671 --rpc --rpcaddr '0.0.0.0' --rpcvhosts=* --ws --wsorigins '*' --wsaddr '0.0.0.0' --wsport 26672 --wsapi 'personal,db,eth,net,web3,txpool,miner,network,debug' --rpcport 26672 --rpcapi 'personal,db,eth,net,web3,txpool,miner,network,debug' --bootnodes 'enode://10b89d31a13d672c3b5b1c27441089e84921c47de304e24ede773cafb935862473250a5f6c8621c738002e47003eefba97999cdfde91c8a1614d1a36f83b8c50@172.19.166.129:0?discport=26690' --networkid 999 --gasprice '1' -unlock '0xFE35176AE93c44C32DE90468fb51Cf1C6AAf0ed8' --password node4/password.txt --mine --allow-insecure-unlock
```

### 其他节点连接

1. 把节点四账户地址发给其他验证节点（比如节点一）

0xfe35176ae93c44c32de90468fb51cf1c6aaf0ed8

```shell script
geth attach node1/geth.ipc

Welcome to the Geth JavaScript console!

instance: Geth/v1.9.24-stable-55636eda/linux-amd64/go1.14.3
coinbase: 0x2d2d68a59880eaacf24b96731956d287482dac4b
at block: 1964 (Tue Nov 24 2020 17:06:52 GMT+0800 (CST))
 datadir: /root/devnet/node1
 modules: admin:1.0 clique:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

To exit, press ctrl-d
> 

```

2. 查看当前验证者集合

```shell script
> clique.getSigners()
["0x0941a01ab7b3a39ed6f55d6a4907778a3f15e5c9", "0x2d2d68a59880eaacf24b96731956d287482dac4b"]
```

3. 添加或者删除验证者节点(需要大于二分之一的节点同意)

```shell script
# true表示加入，false表示删除
# 需要大于二分之一的节点同意，也就是发了这条消息
>clique.propose("0xfe35176ae93c44c32de90468fb51cf1c6aaf0ed8",true)
null
```

4. 取消刚才发送的提案

```shell script
>clique.discard("0x766b2D23a8A7Cd41469A71f55dd6DDe3bCC39616")
null
```

## 连接共识节点的enode接口

### 共识节点提供enode接口

1. 将自己的挖矿账户发给共识节点

2. 共识节点先进入js控制器

```log
geth attach node1/geth.ipc
```

3. 输入以下命令可以查看账户相关的数据

```log
> admin
{
  datadir: "/root/devnet/node1",
  nodeInfo: {
    enode: "enode://cad4ce290e74f0bab8d81b13b825069967ff21c9d3e91a697a24c5585d7168407b70b6000c95065330fe7a261b4ff450bf30bd245305096f500583b814c334c9@127.0.0.1:26681",
    enr: "enr:-Je4QEDwyZOpzmYuXu0BjBSro6GTJjifilzhiXeTjFi6raDgDRU-ECc3NnDkzcvJpgfa0gdCUEJimMr7wZiJzbUCiKcCg2V0aMfGhFcJccWAgmlkgnY0gmlwhH8AAAGJc2VjcDI1NmsxoQPK1M4pDnTwurjYGxO4JQaZZ_8hydPpGml6JMVYXXFoQIN0Y3CCaDmDdWRwgmg5",
    id: "70d538b106863cfe8ecfbd568a107b91144c2c4cf712742fc3c3b3e531e3014a",
    ip: "127.0.0.1",
    listenAddr: "[::]:26681",
    name: "Geth/v1.9.24-stable-55636eda/linux-amd64/go1.14.3",
    ports: {
      discovery: 26681,
      listener: 26681
    },
    protocols: {
      eth: {
        config: {...},
        difficulty: 6604,
        genesis: "0xe13f27b76311ed0ffadff089ba625a26e8feca46b6dfc5d36bd87113b6fd1a59",
        head: "0x751224f50765a72fc15e1da167165cc375f4d68b105e6d971651d819e65b87cc",
        network: 999
      }
    }
  },
  peers: [{
      caps: ["eth/63", "eth/64", "eth/65"],
      enode: "enode://40cd3498d24786f1fdd63a4e55a335d3f05b584e1007f1ae2d58af0196f7fbee518886c2b0ca6773900760d417c08b60e1b2be6da60bb5ecfed2d5fc39eabb52@127.0.0.1:38650",
      id: "65e225742b82635e3c49b83c357e9917ae8ac942421fbb6296bbf3999936d5f1",
      name: "Geth/v1.9.24-stable-55636eda/linux-amd64/go1.14.3",
      network: {
        inbound: true,
        localAddress: "127.0.0.1:26681",
        remoteAddress: "127.0.0.1:38650",
        static: false,
        trusted: false
      },
      protocols: {
        eth: {...}
      }
  }, {
      caps: ["eth/63", "eth/64", "eth/65"],
      enode: "enode://a8a159933b837452322b51ea5e3f9d50de8d9af266564f4702f80247b5fe15247c877c5d7e9286740e41b662845bd1f61b1475d7e2dd51be3d5588179e21423b@127.0.0.1:38636",
      id: "a9d34fc842426288d7df81a30327d6a4aaa5188b8c8e69509cd8712e64d445ce",
      name: "Geth/v1.9.24-stable-55636eda/linux-amd64/go1.14.3",
      network: {
        inbound: true,
        localAddress: "127.0.0.1:26681",
        remoteAddress: "127.0.0.1:38636",
        static: false,
        trusted: false
      },
      protocols: {
        eth: {...}
      }
  }],
  addPeer: function(),
  addTrustedPeer: function(),
  clearHistory: function(),
  exportChain: function(),
  getDatadir: function(callback),
  getNodeInfo: function(callback),
  getPeers: function(callback),
  importChain: function(),
  removePeer: function(),
  removeTrustedPeer: function(),
  sleep: function(),
  sleepBlocks: function(),
  startRPC: function(),
  startWS: function(),
  stopRPC: function(),
  stopWS: function()
}
```

4. 添加节点

```shell script
admin.addPeer('enode://9f6490ffb5236f2ddc5710ae73d47c740e0a3644bbd2d67029cf4a6c4693d2f470b642fd2cc3507f7e851df60aaeb730a1270b7a477f91ec5b6b17a8a4b40527@172.16.0.1:30303')
```

5. 启动自己的交互式JavaScript环境（连接到节点）

```commandline
geth attach node4/geth.ipc
```

## 其他命令

- 同步状态

```shell script
eth.syncing
```

- 列出所有账户

```shell script
> personal.listAccounts
["0xfe35176ae93c44c32de90468fb51cf1c6aaf0ed8"]
```

- 查看默认矿工账户

```shell script
> eth.coinbase
"0xfe35176ae93c44c32de90468fb51cf1c6aaf0ed8"
```

- 查看挂起的交易

```shell script
> eth.pendingTransactions
```

- 显示当前节点信息

```shell script
admin.nodeInfo
```

- 查看节点数量

```shell script
net.peerCount
```

如果返回空值，表示交易全部完成。

## 错误集合

一、出现`invalid merkle root`错误

```log
##############################
 
WARN [11-25|16:04:01.074] Synchronisation failed, dropping peer    peer=31aa8d6c71638483 err="retrieved hash chain is invalid: invalid merkle root (remote: 96b5f123990e42e71c8928f6059bfceb0380e2e57b30a40f00fc14a17c869dbf local: 59de14dd5cb4ee5caeb511bb720866e66dd6ed9bd9778709a949e84850e99ae3)"
INFO [11-25|16:04:01.074] Commit new mining work                   number=10005 sealhash="49ed1a…2149a2" uncles=0 txs=0  gas=0      fees=0        elapsed="143.575µs"
INFO [11-25|16:04:01.074] Signed recently, must wait for others 
INFO [11-25|16:04:01.074] Looking for peers                        peercount=2 tried=2 static=0
INFO [11-25|16:04:01.077] Commit new mining work                   number=10005 sealhash="0c14c3…b4d902" uncles=0 txs=40 gas=840000 fees=8.4e-12  elapsed=2.787ms
INFO [11-25|16:04:01.077] Signed recently, must wait for others 
```

此错误如果不是enode有效问题，可以回滚几个区块试试

比如上面是10005打包出错，重置到10000高度，参数写为其16进制加0x的字符串格式

```log
debug.setHead("0x2710")
null
```



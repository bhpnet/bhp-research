
# 共识节点加入

## 安装geth

[安装geth和部署验证节点教程](./geth.md)

## 添加节点

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

## 启动节点

```shell script
geth --datadir node4/ --syncmode 'full' --gcmode=archive --port 26671 --rpc --rpcaddr '0.0.0.0' --rpcvhosts=* --ws --wsorigins '*' --wsaddr '0.0.0.0' --wsport 26672 --wsapi 'personal,db,eth,net,web3,txpool,miner,network,debug' --rpcport 26672 --rpcapi 'personal,db,eth,net,web3,txpool,miner,network,debug' --bootnodes 'enode://10b89d31a13d672c3b5b1c27441089e84921c47de304e24ede773cafb935862473250a5f6c8621c738002e47003eefba97999cdfde91c8a1614d1a36f83b8c50@172.19.166.129:0?discport=26690' --networkid 999 --gasprice '1' -unlock '0xFE35176AE93c44C32DE90468fb51Cf1C6AAf0ed8' --password node4/password.txt --mine --allow-insecure-unlock
```

## 其他节点连接

把节点四账户地址发给其他验证节点（比如节点一）

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

查看当前验证者集合

```shell script
> clique.getSigners()
["0x0941a01ab7b3a39ed6f55d6a4907778a3f15e5c9", "0x2d2d68a59880eaacf24b96731956d287482dac4b"]
```

添加或者删除验证者节点(需要大于二分之一的节点同意)

```shell script
# true表示加入，false表示删除
# 需要大于二分之一的节点同意，也就是发了这条消息
>clique.propose("0xfe35176ae93c44c32de90468fb51cf1c6aaf0ed8",true)
null
```

取消刚才发送的提案

```shell script
>clique.discard("0x766b2D23a8A7Cd41469A71f55dd6DDe3bCC39616")
null
```


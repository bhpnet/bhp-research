
# geth 客户端私链入门

## 起步

### 1.1 创建目录

```shell script
mkdir devnet
cd devnet
mkdir node1 node2
```

### 1.2 创建账户

```shell script
geth --datadir node1/ account new
geth --datadir node2/ account new
```

将密码保存本地以便后面测试

```shell script
echo 'pwdnode1' > node1/password.txt
echo 'pwdnode2' > node2/password.txt
```

### 1.3 创建 Genesis 文件

```shell script
puppeth
```
- 输入网络名称
```shell script
+-----------------------------------------------------------+
| Welcome to puppeth, your Ethereum private network manager |
|                                                           |
| This tool lets you create a new Ethereum network down to  |
| the genesis block, bootnodes, miners and ethstats servers |
| without the hassle that it would normally entail.         |
|                                                           |
| Puppeth uses SSH to dial in to remote servers, and builds |
| its network components out of Docker containers using the |
| docker-compose toolset.                                   |
+-----------------------------------------------------------+

Please specify a network name to administer (no spaces, hyphens or capital letters please)
> bhp
```
- 选择配置新的genesis
```shell script
What would you like to do? (default = stats)
 1. Show network stats
 2. Configure new genesis
 3. Track new remote server
 4. Deploy network components
> 2
```

- 选择创建新的gensis

```shell script
What would you like to do? (default = create)
 1. Create new genesis from scratch
 2. Import already existing genesis
> 1
```

- 选择poa共识

```shell script
Which consensus engine to use? (default = clique)
 1. Ethash - proof-of-work
 2. Clique - proof-of-authority
> 2
```

- 选择出块时间

```shell script
How many seconds should blocks take? (default = 15)
> 5
```

- 输入刚才创建的两个账户地址，然后按enter进入下一步

```shell script
Which accounts are allowed to seal? (mandatory at least one)
> 0x8b3c63852f2F95964E5F45DC15fbfbDc356792F9
> 0xFA4771E98B786E69a96b03798D240B523932bE4B
```

- 输入刚才的账户提前打钱，然后按enter进入下一步

```shell script
Which accounts should be pre-funded? (advisable at least one)
> 0xFA4771E98B786E69a96b03798D240B523932bE4B
```

- 空账户预先打钱1wei？

```shell script
Should the precompile-addresses (0x1 .. 0xff) be pre-funded with 1 wei? (advisable yes)
> yes
```

- 指定网络id

```shell script
Specify your chain/network ID if you want an explicit one (default = random)
> 999
```

- 选择管理已存在的

```shell script
What would you like to do? (default = stats)
 1. Show network stats
 2. Manage existing genesis
 3. Track new remote server
 4. Deploy network components
> 2
```

- 导出配置

```shell script
 1. Modify existing configurations
 2. Export genesis configurations
 3. Remove genesis configuration
> 2
```

- 直接回车

```
Which folder to save the genesis specs into? (default = current)
  Will create bhp.json, bhp-aleth.json, bhp-harmony.json, bhp-parity.json
> 
INFO [11-19|11:58:33.585] Saved native genesis chain spec          path=bhp.json
ERROR[11-19|11:58:33.585] Failed to create Aleth chain spec        err="unsupported consensus engine"
ERROR[11-19|11:58:33.585] Failed to create Parity chain spec       err="unsupported consensus engine"
INFO [11-19|11:58:33.586] Saved genesis chain spec                 client=harmony path=bhp-harmony.json
```

- 最后Ctrl+C退出

## 初始化节点

```shell script
geth --datadir node1/ init bhp.json
geth --datadir node2/ init bhp.json
```

```shell script
make all
```
把执行文件加到全局配置
```shell script
vim ~/.profile 
source ~/.profile
```

## 创建一个引导节点

引导节点的唯一目的是帮助节点彼此发现

- 初始化bootnode

```shell script
bootnode -genkey boot.key
```


- 启动bootnode服务

```shell script
bootnode -nodekey boot.key -verbosity 9 -addr :26690
```

## 启动节点

```shell script
geth --datadir node1/ --syncmode 'full' --gcmode=archive --port 26681 --rpc --rpcaddr '0.0.0.0' --rpcvhosts=* --ws --wsorigins '*' --wsaddr '0.0.0.0' --wsport 26682 --wsapi 'personal,db,eth,net,web3,txpool,miner,network,debug' --rpcport 26682 --rpcapi 'personal,db,eth,net,web3,txpool,miner,network,debug' --bootnodes 'enode://f51253c9b2a7e21ed63fdacdfef6b6d976598836d202491a6da8c2f4b8a8b450ae5fb187ee0ef39ccd76ff4a9f29a5aa9a3c71b43d534a98808368cec1301083@127.0.0.1:0?discport=26690' --networkid 999 --gasprice '1' -unlock '0x8b3c63852f2F95964E5F45DC15fbfbDc356792F9' --password node1/password.txt --mine --allow-insecure-unlock

geth --datadir node2/ --syncmode 'full' --gcmode=archive --port 26691 --rpc --rpcaddr '0.0.0.0' --rpcvhosts=* --ws --wsorigins '*' --wsaddr '0.0.0.0' --wsport 26692 --wsapi 'personal,db,eth,net,web3,txpool,miner,network,debug' --rpcport 26692 --rpcapi 'personal,db,eth,net,web3,txpool,miner,network,debug' --bootnodes 'enode://f51253c9b2a7e21ed63fdacdfef6b6d976598836d202491a6da8c2f4b8a8b450ae5fb187ee0ef39ccd76ff4a9f29a5aa9a3c71b43d534a98808368cec1301083@127.0.0.1:0?discport=26690' --networkid 999 --gasprice '1' -unlock '0xFA4771E98B786E69a96b03798D240B523932bE4B' --password node2/password.txt --mine --allow-insecure-unlock

```


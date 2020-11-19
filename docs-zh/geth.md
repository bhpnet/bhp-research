
# geth 客户端私链入门

## 一、起步

### 1.1 创建目录

```shell script
mkdir devnet
cd devnet
mkdir node1 node2

# 下载geth
git clone https://gitee.com/bhpnet/go-ethereum.git

cd go-ethereum

# 切换到最新稳定分支
git checkout v1.9.24

# 编译执行文件
make all

# 把执行文件加到全局配置
vim ~/.profile 
```

- 添加目录然后保存
```
export PATH=$PATH:/root/devnet/go-ethereum/build/bin
```

- 刷新
```
source ~/.profile
```

- 查看版本

```shell script
geth version

Geth
Version: 1.9.24-stable
Git Commit: cc05b050df5f88e80bb26aaf6d2f339c49c2d702
Architecture: amd64
Protocol Versions: [65 64 63]
Go Version: go1.14.3
Operating System: linux
GOPATH=/root/goApps
GOROOT=go
```

### 1.2 创建账户

在`devnet`目录下分别创建账户（记住地址和密码）

geth --datadir node1/ account new

```log
# 日志
Your new account is locked with a password. Please give a password. Do not forget this password.
Password: 
Repeat password: 

Your new key was generated

Public address of the key:   0xD68066d2292b9e80FdE6904447A044050ca3fA3C
Path of the secret key file: node1/keystore/UTC--2020-11-19T08-31-02.605416348Z--d68066d2292b9e80fde6904447a044050ca3fa3c

- You can share your public address with anyone. Others need it to interact with you.
- You must NEVER share the secret key with anyone! The key controls access to your funds!
- You must BACKUP your key file! Without the key, it's impossible to access account funds!
- You must REMEMBER your password! Without the password, it's impossible to decrypt the key!
```
geth --datadir node2/ account new

```log
# 日志
Your new key was generated

Public address of the key:   0x30439478508367F4B8dBEC1Df0a1D61169b5a4d1
Path of the secret key file: node2/keystore/UTC--2020-11-19T08-35-07.765933402Z--30439478508367f4b8dbec1df0a1d61169b5a4d1

- You can share your public address with anyone. Others need it to interact with you.
- You must NEVER share the secret key with anyone! The key controls access to your funds!
- You must BACKUP your key file! Without the key, it's impossible to access account funds!
- You must REMEMBER your password! Without the password, it's impossible to decrypt the key!
```

将密码保存本地以便后面测试，本案例测试密码为12341234

```shell script
echo '12341234' > node1/password.txt
echo '12341234' > node2/password.txt
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
> genesis
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

- 选择创建新的genesis

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

- 哪些帐户应预先注资？（建议至少一个）

> 同样，在开发环境中，预先分配多少地址并不重要。但是，如果用于商业用途，请谨慎执行此步骤（您无法决定要投入多少资金）。
  当您进行交易，部署智能合约，发布ERC-20令牌等时，将使用资金。

```shell script
Which accounts should be pre-funded? (advisable at least one)
> 0xFA4771E98B786E69a96b03798D240B523932bE4B
```

- 预编译地址（0x1 .. 0xff）是否应预先充值1 wei？（建议是）

```shell script
Should the precompile-addresses (0x1 .. 0xff) be pre-funded with 1 wei? (advisable yes)
> yes
```

- 指定网络id

```shell script
Specify your chain/network ID if you want an explicit one (default = random)
> 999
```

- 管理已存在的genesis

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
> 2ll

```
- 直接回车

将genesis保存到哪个文件夹？（默认=当前）

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


## 二、初始化节点

```shell script
geth --datadir node1/ init genesis.json
geth --datadir node2/ init genesis.json
```

## 三、创建一个引导节点

引导节点的唯一目的是帮助节点彼此发现

- 初始化bootnode

```shell script
bootnode -genkey boot.key
```


- 启动bootnode服务

```shell script
bootnode -nodekey boot.key -verbosity 9 -addr :26690
```
显示并获取bootnode的enode信息
```log
bootnode -nodekey boot.key -verbosity 9 -addr :26690
enode://7f6bf28538ce1c28112483a7776de8af8bb26ece7f54e1545dc379f15e662aba49f60d662559e9e7389d66f8e31b570f4a0c5fc7e1bd84548270099de05d0c12@127.0.0.1:0?discport=26690
Note: you're using cmd/bootnode, a developer tool.
We recommend using a regular node as bootstrap node for production deployments.
INFO [11-19|16:42:14.896] New local node record                    seq=1 id=eaae3942df1dc2e5 ip=<nil> udp=0 tcp=0
```

提取出来
```log
enode://7f6bf28538ce1c28112483a7776de8af8bb26ece7f54e1545dc379f15e662aba49f60d662559e9e7389d66f8e31b570f4a0c5fc7e1bd84548270099de05d0c12@127.0.0.1:0?discport=26690
```

## 四、启动节点

```commandline
# 启动部分命令
geth --datadir node1/ --syncmode 'full' --port 26681 --rpc --rpcaddr '0.0.0.0' --rpcport 26682 --rpcapi 'personal,db,eth,net,web3,txpool,miner,admin' --rpccorsdomain '*' --networkid 999 --bootnodes "enode://7f6bf28538ce1c28112483a7776de8af8bb26ece7f54e1545dc379f15e662aba49f60d662559e9e7389d66f8e31b570f4a0c5fc7e1bd84548270099de05d0c12@127.0.0.1:0?discport=26690" --gasprice '1' --unlock '0xD68066d2292b9e80FdE6904447A044050ca3fA3C'  --password node1/password.txt  --mine --allow-insecure-unlock

geth --datadir node2/ --syncmode 'full' --port 26691 --rpc --rpcaddr '0.0.0.0' --rpcport 26692 --rpcapi 'personal,db,eth,net,web3,txpool,miner,admin' --rpccorsdomain '*' --networkid 999 --bootnodes "enode://7f6bf28538ce1c28112483a7776de8af8bb26ece7f54e1545dc379f15e662aba49f60d662559e9e7389d66f8e31b570f4a0c5fc7e1bd84548270099de05d0c12@127.0.0.1:0?discport=26690" --gasprice '1' --unlock '0x30439478508367F4B8dBEC1Df0a1D61169b5a4d1'  --password node2/password.txt  --mine --allow-insecure-unlock

# 启动全面命令

geth --datadir node1/ --syncmode 'full' --gcmode=archive --port 26681 --rpc --rpcaddr '0.0.0.0' --rpcvhosts=* --ws --wsorigins '*' --wsaddr '0.0.0.0' --wsport 26682 --wsapi 'personal,db,eth,net,web3,txpool,miner,network,debug' --rpcport 26682 --rpcapi 'personal,db,eth,net,web3,txpool,miner,network,debug' --bootnodes 'enode://7f6bf28538ce1c28112483a7776de8af8bb26ece7f54e1545dc379f15e662aba49f60d662559e9e7389d66f8e31b570f4a0c5fc7e1bd84548270099de05d0c12@127.0.0.1:0?discport=26690' --networkid 999 --gasprice '1' -unlock '0xD68066d2292b9e80FdE6904447A044050ca3fA3C' --password node1/password.txt --mine --allow-insecure-unlock

geth --datadir node2/ --syncmode 'full' --gcmode=archive --port 26691 --rpc --rpcaddr '0.0.0.0' --rpcvhosts=* --ws --wsorigins '*' --wsaddr '0.0.0.0' --wsport 26692 --wsapi 'personal,db,eth,net,web3,txpool,miner,network,debug' --rpcport 26692 --rpcapi 'personal,db,eth,net,web3,txpool,miner,network,debug' --bootnodes 'enode://971be5aa523115af627483c2c4902079fd9479adeacdc7f59645e49cdbe1d5692754030b4a4d20d318a4b7b15687f12712e15d3545180172115d213d4be1532f@127.0.0.1:26681' --networkid 999 --gasprice '1' -unlock '0x30439478508367F4B8dBEC1Df0a1D61169b5a4d1' --password node2/password.txt --mine --allow-insecure-unlock

```


## 参考链接

```log
https://hackernoon.com/setup-your-own-private-proof-of-authority-ethereum-network-with-geth-9a0a3750cda8
https://medium.com/@david.chou93/deploy-a-private-ethereum-test-network-b181d1589681
```


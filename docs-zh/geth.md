
# geth 客户端私链入门

bhpNet：所有修改了的地方
bhpDM：后期可能还要修改的地方
bhpNot：暂时不用的

## 一、起步

首先安装golang1.15+ 和git

> 注意：国内安装需要设置go代理：

```shell script
echo "export GO111MODULE=on" >> ~/.profile
echo "export GOPROXY=https://goproxy.cn" >> ~/.profile
source ~/.profile
```

### 1.1 创建目录

```shell script
mkdir devnet
cd devnet
mkdir node1 node2

# 下载geth
git clone https://gitee.com/bhpnet/bhpeth.git

cd bhpeth

# 切换到最新稳定分支
git checkout v1.9.24-bhp3

# 编译执行文件
make all

# 把执行文件位置加到全局配置
vim /etc/profile
```

- 添加此变量然后保存
```
# /root/devnet/bhpnet/build/bin为编译后执行文件的目录，仅供参考
export PATH=$PATH:/root/devnet/bhpnet/build/bin
```

- 刷新
```
source /etc/profile
```

- 查看版本(注意Commit记录要一致)

```shell script
Geth
Version: 1.9.24-stable
Git Commit: 941620e90616ddb17ec4663f1b03ade1a410e3dd
Architecture: amd64
Protocol Versions: [65 64 63]
Go Version: go1.14.3
Operating System: linux
GOPATH=/root/goApps
GOROOT=go
You have new mail in /var/mail/root
```

在`devnet`目录下执行以下命令：将密码保存本地以便后面测试，本案例测试密码为12341234


```shell script
echo '12341234' > node1/password.txt
echo '12341234' > node2/password.txt
```

### 1.2 创建账户

在`devnet`目录下分别创建账户（记住地址和密码）

在node1中生成账户

```shell script
geth --datadir node1/ account new --password node1/password.txt
```

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

在node2中生成账户

```shell script
geth --datadir node2/ account new --password node2/password.txt
```

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

## 部署Ethstats(这步可以暂时跳过)

```log
puppeth -network genesis

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

INFO [11-20|10:43:27.530] Administering Ethereum network           name=genesis
INFO [11-20|10:43:27.532] No remote machines to gather stats from 

What would you like to do? (default = stats)
 1. Show network stats
 2. Manage existing genesis
 3. Track new remote server
 4. Deploy network components
> 4

What would you like to deploy? (recommended order)
 1. Ethstats  - Network monitoring tool
 2. Bootnode  - Entry point of the network
 3. Sealer    - Full node minting new blocks
 4. Explorer  - Chain analysis webservice
 5. Wallet    - Browser wallet for quick sends
 6. Faucet    - Crypto faucet to give away funds
 7. Dashboard - Website listing above web-services
> 1

Which server do you want to interact with?
 1. Connect another server
> 1

What is the remote server's address ([username[:identity]@]hostname[:port])?
> root@101.133.225.179
WARN [11-20|10:49:33.222] No SSH key, falling back to passwords    path=/root/.ssh/id_rsa err="open /root/.ssh/id_rsa: no such file or directory"

The authenticity of host '101.133.225.179:22 (101.133.225.179:22)' can't be established.
SSH key fingerprint is 4b:5c:f9:e5:34:b6:81:f6:8e:b8:0f:e6:83:f8:9b:93 [MD5]
Are you sure you want to continue connecting (yes/no)? yes
What's the login password for root at root@101.133.225.179? (won't be echoed)
> 

Which port should ethstats listen on? (default = 80)
> 26682

Allow sharing the port with other services (y/n)? (default = yes)
> yes
INFO [11-20|10:50:30.976] Deploying nginx reverse-proxy            server=101.133.225.179 port=26682
Creating network "genesis_default" with the default driver
Building nginx
Step 1/1 : FROM jwilder/nginx-proxy
latest: Pulling from jwilder/nginx-proxy
Digest: sha256:695db064e3c07ed052ea887b853ffba07e8f0fbe96dc01aa350a0d202746926b
Status: Downloaded newer image for jwilder/nginx-proxy:latest
 ---> 509ff2fb81dd
Successfully built 509ff2fb81dd
Successfully tagged genesis/nginx:latest
Creating genesis_nginx_1 ... done

Proxy deployed, which domain to assign? (default = 101.133.225.179)
>
```

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

-    --networkid value                   Explicitly set network id (integer)(For testnets: use --ropsten, --rinkeby, --goerli instead) (default: 1)
-    --datadir value                     Data directory for the databases and keystore (default: "/root/.ethereum")
-    --bootnodes value                   Comma separated enode URLs for P2P discovery bootstrap
-    --http                              Enable the HTTP-RPC server
-    --http.addr value                   HTTP-RPC server listening interface (default: "localhost")
-    --http.port value                   HTTP-RPC server listening port (default: 8545)
-    --http.api value                    API's offered over the HTTP-RPC interface
-    --http.corsdomain value             Comma separated list of domains from which to accept cross origin requests (browser enforced)
-    --http.vhosts value                 Comma separated list of virtual hostnames from which to accept requests (server enforced). Accepts '*' wildcard. (default: "localhost")
-    --ws                                Enable the WS-RPC server
-    --ws.addr value                     WS-RPC server listening interface (default: "localhost")
-    --ws.port value                     WS-RPC server listening port (default: 8546)
-    --ws.api value                      API's offered over the WS-RPC interface
-    --ws.origins value                  Origins from which to accept websockets requests
-    --mine                              Enable mining
-    --unlock value                      Comma separated list of accounts to unlock
-    --password value                    Password file to use for non-interactive password input
-    --signer value                      External signer (url or path to ipc file)
-    --allow-insecure-unlock             Allow insecure account unlocking when account-related RPCs are exposed by http


```shell script
# 启动部分命令
geth --datadir node1/ --syncmode 'full' --port 26681 --http --http.addr '0.0.0.0' --http.port 26682 --http.vhosts '*' --networkid 999 --bootnodes "enode://7f6bf28538ce1c28112483a7776de8af8bb26ece7f54e1545dc379f15e662aba49f60d662559e9e7389d66f8e31b570f4a0c5fc7e1bd84548270099de05d0c12@127.0.0.1:0?discport=26690" --gasprice '1' --unlock '0xD68066d2292b9e80FdE6904447A044050ca3fA3C'  --password node1/password.txt  --mine --allow-insecure-unlock

geth --datadir node2/ --syncmode 'full' --port 26691 --http --http.addr '0.0.0.0' --http.port 26692 --http.vhosts '*' --networkid 999 --bootnodes "enode://7f6bf28538ce1c28112483a7776de8af8bb26ece7f54e1545dc379f15e662aba49f60d662559e9e7389d66f8e31b570f4a0c5fc7e1bd84548270099de05d0c12@127.0.0.1:0?discport=26690" --gasprice '1' --unlock '0x30439478508367F4B8dBEC1Df0a1D61169b5a4d1'  --password node2/password.txt  --mine --allow-insecure-unlock

# 启动全面命令

geth --datadir node1/ --syncmode 'full' --gcmode=archive --port 26681 --http --http.addr '0.0.0.0' --http.port 26682 --http.vhosts '*' --ws --ws.addr '0.0.0.0' --ws.port 26682 --ws.origins '*' --bootnodes "enode://7f6bf28538ce1c28112483a7776de8af8bb26ece7f54e1545dc379f15e662aba49f60d662559e9e7389d66f8e31b570f4a0c5fc7e1bd84548270099de05d0c12@127.0.0.1:0?discport=26690" --networkid 999 --gasprice '1' -unlock '0xD68066d2292b9e80FdE6904447A044050ca3fA3C' --password node1/password.txt --mine --allow-insecure-unlock

geth --datadir node2/ --syncmode 'full' --gcmode=archive --port 26691 ---http --http.addr '0.0.0.0' --http.port 26692 --http.vhosts '*' --ws --ws.addr '0.0.0.0' --ws.port 26682 --ws.origins '*' --bootnodes "enode://7f6bf28538ce1c28112483a7776de8af8bb26ece7f54e1545dc379f15e662aba49f60d662559e9e7389d66f8e31b570f4a0c5fc7e1bd84548270099de05d0c12@127.0.0.1:0?discport=26690" --networkid 999 --gasprice '1' -unlock '0x30439478508367F4B8dBEC1Df0a1D61169b5a4d1' --password node2/password.txt --mine --allow-insecure-unlock

```

## 五、节点连接

[加入共识节点](./geth_node.md)

## 参考链接

```log
https://hackernoon.com/setup-your-own-private-proof-of-authority-ethereum-network-with-geth-9a0a3750cda8
https://medium.com/@david.chou93/deploy-a-private-ethereum-test-network-b181d1589681
```

## geth 常用命令

- 启动交互式JavaScript环境（连接到节点）

```shell
geth attach node/geth.ipc
```

- 启动bhp测试网

```shell
geth --datadir node --bhp
```

- 回滚数据

参数为16进制，比如区块编号：37550，0x92ae

```shell
debug.setHead("0x92ae")
```

- 添加节点

```shell
admin.addPeer("enode://bf30bfe46abf05e5231dfa580118d04b626086f64cfffe617302e07585a3d9075428cf9d1d97506a02091e0d45a599cfb67808f89cdc2796d81a42e03498c9fa@47.103.38.41:26693")
```


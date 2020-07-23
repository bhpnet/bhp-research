## 部署私有的okchain测试网
### 一、部署单节点
#### 前提
- [安装okchain](install-okchain.md )
#### 创建genesis.json文件并启动网络
```shell script
# You can run all of these commands from your home directory
cd $HOME

# Initialize the genesis.json file that will help you to bootstrap the network
okchaind init --chain-id=testing testing

# 创建一个钱包作为您的验证器帐户
okchaincli keys add validator

# 将该钱包地址添加到genesis文件中的genesis.app_state.accounts数组中
# 注意: 此命令使您可以设置通证数量。确保此帐户有币，这是测试网络上唯一的质押通证
# with the genesis.app_state.staking.params.bond_denom denom, the default is staking
okchaind add-genesis-account $(okchaincli keys show validator -a) 1000000000okt,1000000000tokt

# 生成创建验证器的交易，gentx存储在~/.okchaind/config/中
okchaind gentx --name validator

# 将生成的质押交易添加到创世文件：Add the generated bonding transaction to the genesis file
okchaind collect-gentxs

# 现在可以启动okchaind了：Now its safe to start `okchaind`
okchaind start
```

### 二、部署多节点
#### 前提
- [安装okchain](install-okchain.md )
- [安装docker](install-docker.md )
- [安装docker-compose](install-docker-compose.md )
```shell script
# 克隆okchain仓库
git clone https://github.com/okex/okchain.git

# Work from the SDK repo
cd okchain

# Build the linux binary in ./build
make build-linux

# 构建okchain/node镜像
make build-docker-okchainnode
```
#### 启动
启动4个网络节点(此方式的钱包默认密码是`12345678`)
```shell script
make localnet-start
```
该命令使用gaiadnode映像创建4个节点网络。下表中是每个节点对应的端口：

|  节点编号   | P2P端口  | RPC端口  |
|  ----  | ----  | ----  |
| okchainnode0  | 26656 | 26657 |
| okchainnode1  | 26659 | 26660 |
| okchainnode2  | 26661 | 26662 |
| okchainnode3  | 26663 | 26664 |
- 如果更新了二进制文件版本，只需重新购建它并重新启动节点即可：
```shell script
make build-linux localnet-start
```
#### 结构
`make localnet-start`命令会调用`okchaind testnet`命令在 `./build` 目录下生成4个节点的测试网配置文件。
```shell script
$ tree -L 3 build/
build/
├── okchaincli
├── okchaind
├── gentxs
│   ├── node0.json
│   ├── node1.json
│   ├── node2.json
│   └── node3.json
├── node0
│   ├── okchaincli
│   │   ├── key_seed.json
│   │   └── keys
│   └── okchaind
│       ├── okchaind.log
│       ├── config
│       └── data
├── node1
│   ├── okchaincli
│   │   └── key_seed.json
│   └── okchaind
│       ├── okchaind.log
│       ├── config
│       └── data
├── node2
│   ├── okchaincli
│   │   └── key_seed.json
│   └── okchaind
│       ├── okchaind.log
│       ├── config
│       └── data
└── node3
    ├── okchaincli
    │   └── key_seed.json
    └── okchaind
        ├── okchaind.log
        ├── config
        └── data
```
#### 日志
日志保存在每个节点`./build/nodeN/okchaind/okchaind.log`。您还可以直接通过Docker查看日志，例如：
```shell script
docker logs -f okchaindnode0
```
#### 密钥和账户
- 返回此密钥管理器存储的所有密钥的名称、类型、地址和公钥列表。
```shell script
okchaincli keys list --home ./build/node0/okchaincli
```
- 创建钱包
系统将随机生成包括助记词、公钥和地址在内的信息。
```shell script
okchaincli keys add <name> [flags]
```
成功响应：
```shell script
okchaincli keys add node4

# Example output
{
    "name":"node4",
    "type":"local",
    "address":"okchain1lfeaxj0z2sefwgcac2fy5c7j4a0lppkmmhzflm",
    "pubkey":"okchainpub1addwnpepqdct9auscyamuqc3nc6sv935847hl5knlk7ed43z9jl6zr9njmy85373tuv",
    "mnemonic":"vocal copy mosquito earn nest ranch someone dragon kiss they govern bronze"
}
```
现在帐户已经存在，您可以创建新帐户并向这些帐户发送资金！
>注意：每个节点的种子位于以下位置，`./build/nodeN/okchaincli/key_seed.json`
### 三、普通节点加入自己部署的测试网络
设置一个新的节点
>注意：Monikers只能包含ASCII字符。使用Unicode字符会导致您的节点无法访问。
```shell script
okchaind init <your_custom_moniker>
```
您可以在`~/.okchaind/config/config.toml`文件中进行编辑`moniker`
```shell script
# A custom human readable name for this node
moniker = "<your_custom_moniker>"
```
`~/.okchaind/config/okchaind.toml`
```shell script
# This is a TOML config file.
# For more information, see https://github.com/toml-lang/toml

##### main base config options #####

# The minimum gas prices a validator is willing to accept for processing a
# transaction. A transaction's fees must meet the minimum of any denomination
# specified in this config (Our recommended quantity is  10^-7 tokt).

minimum-gas-prices = ""
```
至此，您的全节点已初始化！
#### Genesis & Seeds
将`testnet`的`genesis.json`文件提取到`okchaind`的config目录中。

请注意

复制genesis文件

要验证配置的正确性，请执行以下操作：
```shell script
okchaind start
```
#### 添加种子节点
您需要向添加健康种子节点`$HOME/.okchaind/config/config.toml`。
```shell script
# Comma separated list of seed nodes to connect to
seeds = "b7c6bdfe0c3a6c1c68d6d6849f1b60f566e189dd@3.13.150.20:36656,d7eec05e6449945c8e0fd080d58977d671eae588@35.176.111.229:36656,223b5b41d1dba9057401def49b456630e1ab2599@18.162.106.25:36656"
```
#### 启动节点
使用以下命令启动全节点：
```shell script
okchaind start
```
检查一切运行是否顺利：``
```shell script
okchaincli status
```
#### 升级节点版本
这些说明适用于在的早期版本上运行并且想要升级到最新测试网的全节点。
- 重置数据
首先，删除过时的文件并重置数据
```shell script
rm $HOME/.okchaind/config/addrbook.json $HOME/.okchaind/config/genesis.json
okchaind unsafe-reset-all
```
您的节点现在处于原始状态，同时保持原始`priv_validator.json`和`config.toml`。如果您之前设置了任何哨兵节点或全节点，则您的节点仍将尝试连接到它们，但是如果还没有升级它们，则可能会失败。
>注意：确保每个节点都有唯一的`priv_validator.json`。不要将其`priv_validator.json`从旧节点复制到多个新节点。使用相同的节点运行两个节点`priv_validator.json`将导致您双重登录。
#### 软件升级
```shell script
git clone https://github.com/okex/okchain.git
cd okchain
git fetch --all && git checkout master
make install
```
>注意：如果在此步骤中遇到问题，请检查是否已安装最新的稳定版GO。
### 四、普通节点升级为验证器节点
请参阅[验证器指南](./okchain-validator.md )。
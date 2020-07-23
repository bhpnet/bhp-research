## OKChain验证器指南
#### 创建钱包
- 创建一个新的密钥（钱包），或通过助记词/密钥库导入已有密钥。执行该命令后输入并确认密码，将生成一个新的密钥。密码至少8个字符。


#### 创建验证器
只有节点已完成同步时，才可以运行以下命令将您的节点升级为验证器：
```shell script
okchaincli tx staking create-validator \
 --pubkey=$(okchaind tendermint show-validator) \
 --moniker="my nickname" \
 --identity="logo|||http://mywebsite/pic/logo.jpg" \
 --website="http://mywebsite" \
 --details="my slogan" \
 --from jack
```
例如：
```shell script
okchaincli tx staking create-validator \
 --pubkey=$(okchaind tendermint show-validator) \
 --moniker="node4" \
 --identity="logo|||http://bhpa.io/logo.jpg" \
 --website="http://bhpa.io" \
 --details="bhpa" \
 --chain-id=chain-u00LC1 \

 --from node4
```
>注意: 如果你执行 `okchaind start`设置了home flag, `okchaind tendermint show-validator` 这里也应该和 `okcahind start`一样设置home标签
- pubkey：当前节点的tendermint公钥。
- moniker：验证器的别名
- identity：验证器的个人资料图片地址
- website：验证器的网站地址
- details：验证器的详细说明
- from：操作者的账户

#### 更新验证器信息
操作员可以更新验证器的描述并调整佣金率。
```shell script
okchaincli tx staking edit-validator \
 --moniker="my new nickname" \
 --identity="logo|||http://mynewwebsite/pic/newlogo.jpg" \
 --website="http://mynewwebsite" \
 --details="my new slogan" \
 --from jack
```
#### 存款
用户需要在抵押账户中存入一定数量的okt才能成为委托人
```shell script
okchaincli tx staking deposit <amountToDeposit> --from <delegatorKeyName> --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice>
```
#### 添加shares

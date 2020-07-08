## Ethermint 部分命令使用指南
- 删除数据

此命令会删除守护程序`.emintd`和客户端`.emintcli`目录
```shell script
rm -rf ~/.emint*
```
- 重置数据

可以使用此命令来重置节点，包括本地区块链数据库，地址簿文件，并将priv_validator.json重置为创世状态。

当本地区块链数据库以某种方式中断和无法同步或参与共识时，这是有用的。
```shell script
emintd unsafe-reset-all
```
- 查看网络状态信息
```shell script
 netstat -nptl
```
- 导出私钥
```shell script
emintcli keys unsafe-export-eth-key mykey
```
- 创建密钥

用法
```shell script
emintcli keys add <name> [flags]
```
执行该命令后输入并确认密码，将生成一个新的密钥。密码至少8个字符。
>注意
>
>重要
>
>写下助记词并保存在安全的地方！如果你不慎忘记密码或丢失了密钥，这是恢复账户的唯一方法。
- 列出所有密钥

返回此密钥管理器存储的所有密钥的名称、类型、地址和公钥列表。
```shell script
emintcli keys list
```
- 查询本地密钥的详细信息。
用法
```shell script
emintcli keys show [name_or_address [name_or_address...]] [flags]
```
示例
```shell script
emintcli keys show node3
Enter keyring passphrase:
{
  "name": "node3",
  "type": "local",
  "address": "cosmos1pjpy6sjgspyn26vmdgs2xpg964w89sq5xmtaq5",
  "pubkey": "cosmospub179jcuvjpqjtgpx07ftd03gd6pzvt0r4autes2gvtdj2qln76anf9k2cm5yst9tldeq55mj7h2jkx3h5xstzqwdjvddxexy33hq795anmssyy0snueh53yp"
}
```
- 导出密钥

用法
```shell script
emintcli keys export <name> [flags]
```
示例
```shell script
emintcli keys export node3
Enter passphrase to decrypt your key:
Enter passphrase to encrypt the exported key:
Enter keyring passphrase:
-----BEGIN TENDERMINT PRIVATE KEY-----
kdf: bcrypt
salt: FA775808EB4A6B35D52CEFC8529AF25D
type: secp256k1

7txMv7I/utyxta+E23Csvj06wxex7g06+OYLyGvAjLM3tiA+qEGUR0w1bCTF9b2KgoMXZsuVpHan1ruH52jWbyXlH0wbsm5vEF5W7R8==0c7L
-----END TENDERMINT PRIVATE KEY-----
```
- 删除密钥

用法
```shell script
emintcli keys delete <name>... [flags]
```
示例
```shell script
emintcli keys delete node3
Enter keyring passphrase:
Key deleted forever (uh oh!)
```
- 导入密钥

用法
```shell script
emintcli keys import <name> <keyfile> [flags]
```
```shell script

```
- 转帐

用法
```shell script
emintcli tx send [from_key_or_address] [to_address] [amount] [flags]
```
示例
```shell script
emintcli tx send cosmos1lpa40757ka492ewy225qmmdcwr3gmdacmjm0yt cosmos1hwp0fss8ngvfrjtsdhxhfrh96xtmclvd36tyk8 2000000stake --chain-id 2020 --fees=2stake
```
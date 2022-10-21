# Token创建文档

## 钱包准备

### 生成seed

```bash
./ybkchain-cli seed generate -l <lang>
```
lang: seed语言种类，0:English，1:简体中文


### 导入seed并设置密码

```bash
./ybkchain-cli seed save -s <seed> -p <pwd>
```

### 解锁钱包

```bash
./ybkchain-cli wallet unlock -p <password>
```

### 开启自动挖矿
```bash
./ybkchain-cli wallet auto_mine -f <flag>
```

flag: 自动挖矿标识符， 0: 关闭    1: 开启


## token预配置

### 检查超级管理员

超级管理员的作用是增加系统配置，比如下面的token-blacklist, token-finisher的配置。
在ybkchain.toml中修改添加以下两项配置，把这两个地址改成自己想设置的地址。

```bash
[exec.sub.token]
saveTokenTxList = true
tokenApprs = [
    "13qaX6PZpVj8bK3Nrv5jWR629WLrjNUtbu",
    "1Cj8ks3H7Rc2pRpsuZrANm1wM2PTL66xcA",
]
[exec.sub.manage]
superManager = [
    "13qaX6PZpVj8bK3Nrv5jWR629WLrjNUtbu",
    "1Cj8ks3H7Rc2pRpsuZrANm1wM2PTL66xcA"
]
```

### 配置token-finisher

构建token-finisher配置交易

```bash
./ybkchain-cli config config_tx -c token-finisher -o add -v <address>
```

address：需要添加token-finisher权限的地址

交易签名

```bash
./ybkchain-cli wallet sign -d <待签名的交易>
```

交易发送

```bash
./ybkchain-cli wallet send -d <签名后的交易>
```

待交易上链后，验证账户finisher权限是否配置成功

```bash
./ybkchain-cli config query_config -k "token-finisher"
{
    "key": "token-finisher",
    "value": "[13qaX6PZpVj8bK3Nrv5jWR629WLrjNUtbu]"
}
```


### 配置token-blacklist

构建token-blacklsit配置交易

```bash
./ybkchain-cli config config_tx -c token-blacklist -o add -v <tokenName>
```

tokenName：需要进入token黑名单的token名称，例如主链币为YBK，则需要将YBK加到黑名单中避免后续创建同名token

交易签名

```bash
./ybkchain-cli wallet sign -d <待签名的交易>
```

交易发送

```bash
./ybkchain-cli wallet send -d <签名后的交易>
```

待交易上链后，验证token黑明单中YBK是否配置成功

```
./ybkchain-cli config query_config -k "token-blacklist"
{
    "key": "token-blacklist",
    "value": "[YBK]"
}
```

## 发行token

### token precreate预创建

```
./ybkchain-cli token precreate -h
Create a precreated token transaction

Usage:
  -cli token precreate [flags]

Flags:
  -c, --category int32        token category
  -f, --fee float             token transaction fee
  -h, --help                  help for precreate
  -i, --introduction string   token introduction
  -n, --name string           token name
  -a, --owner_addr string     address of token owner
  -p, --price float           token price(mini: 0.0001)
  -s, --symbol string         token symbol
  -t, --total int             total amount of the token

Global Flags:
      --paraName string    parachain
      --rpc_laddr string   http url (default "https://localhost:8801")
```

样例

```bash
./ybkchaincli token precreate -f 0.001 -i "ctc token" -n "ctc" -a "13qaX6PZpVj8bK3Nrv5jWR629WLrjNUtbu" -p 0 -s "CTC" -t 100000000
```

签名交易

```bash
./ybkchain-cli wallet sign -k 13qaX6PZpVj8bK3Nrv5jWR629WLrjNUtbu -d <上一步生成的签名后的数据>
```

发送交易

```bash
./ybkchain-cli wallet send -d <上一步生成的签名后的数据>
```

###  验证token 预创建
```bash
./ybkchain-cli token get_precreated
{
    "name": "ctc",
    "symbol": "CTC",
    "introduction": "ctc token",
    "total": 10000000,
    "owner": "13qaX6PZpVj8bK3Nrv5jWR629WLrjNUtbu",
    "creator": "13qaX6PZpVj8bK3Nrv5jWR629WLrjNUtbu",
    "prepareCreateHeight": 407,
    "prepareCreateTime": 1665971591,
    "precision": 8
}
```

### token finish 完成创建

```bash
 ./ybkchain-cli token finish -h
Create a finish created token transaction

Usage:
  -cli token finish [flags]

Flags:
  -f, --fee float           token transaction fee
  -h, --help                help for finish
  -a, --owner_addr string   address of token owner
  -s, --symbol string       token symbol

Global Flags:
      --paraName string    parachain
      --rpc_laddr string   http url (default "https://localhost:8801")
```

样例

```bash
./ybkchain-cli token finish -s CTC -f 0.001 -a "token接收者的地址"
```

签名交易

```bash
./ybkchain-cli wallet sign -k 13qaX6PZpVj8bK3Nrv5jWR629WLrjNUtbu -d <上一步生成的签名后的数据>
```
发送签名后的交易

```bash
./ybkchain-cli wallet send -d <上一步生成的签名后的数据>
```
###  验证token 完成创建

```bash
./ybkchain-cli token get_finish_created
{
    "name": "ctc",
    "symbol": "CTC",
    "introduction": "ctc token",
    "total": 10000000,
    "owner": "13qaX6PZpVj8bK3Nrv5jWR629WLrjNUtbu",
    "creator": "13qaX6PZpVj8bK3Nrv5jWR629WLrjNUtbu",
    "status": 1,
    "createdHeight": 409,
    "createdTime": 1665971797,
    "prepareCreateHeight": 407,
    "prepareCreateTime": 1665971591,
    "precision": 8
}
```

### 根据地址查询

```bash
./ybkchain-cli token token_balance -a 13qaX6PZpVj8bK3Nrv5jWR629WLrjNUtbu  -s CTC -e token
[
    {
        "Token": "CTC",
        "balance": "10000000.0000",
        "frozen": "0.0000",
        "addr": "13qaX6PZpVj8bK3Nrv5jWR629WLrjNUtbu"
    }
]
```

## token转账

构建token转账交易

```bash
./ybkchain-cli token transfer -a 1000 -t 1Cj8ks3H7Rc2pRpsuZrANm1wM2PTL66xcA -s CTC
```

签名交易

```bash
./ybkchain-cli wallet sign -k 13qaX6PZpVj8bK3Nrv5jWR629WLrjNUtbu -d <上一步生成的签名后的数据>
```
发送签名后的交易

```bash
./ybkchain-cli wallet send -d <上一步生成的签名后的数据>
```

待交易成功上链后，查看接受方地址余额

```
./ybkchain-cli token token_balance -a 1Cj8ks3H7Rc2pRpsuZrANm1wM2PTL66xcA -s CTC -e token
[
    {
        "Token": "CTC",
        "balance": "1000.0000",
        "frozen": "0.0000",
        "addr": "1Cj8ks3H7Rc2pRpsuZrANm1wM2PTL66xcA"
    }
]
```
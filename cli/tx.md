`JD Chain` 命令行交易工具集

```bash
:bin$ ./jdchain-cli.sh tx -h
Usage: jdchain-cli tx [-hV] [--gw-secure] [--pretty] [--export=<export>]
                      [--gw-host=<gwHost>] [--gw-port=<gwPort>] [--home=<path>]
                      [--ssl.ciphers=<ciphers>] [--ssl.
                      enabled-protocols=<enabledProtocols>] [--ssl.
                      host-verifier=<hostNameVerifier>] [--ssl.
                      key-alias=<keyAlias>] [--ssl.key-store=<keyStore>] [--ssl.
                      key-store-password=<keyStorePassword>] [--ssl.
                      key-store-type=<keyStoreType>] [--ssl.
                      protocol=<protocol>] [--ssl.trust-store=<trustStore>]
                      [--ssl.trust-store-password=<trustStorePassword>] [--ssl.
                      trust-store-type=<trustStoreType>] [COMMAND]
Build, sign or send transaction.
      --export=<export>    Transaction export directory
      --gw-host=<gwHost>   Set the gateway host. Default: 127.0.0.1
                             Default: 127.0.0.1
      --gw-port=<gwPort>   Set the gateway port. Default: 8080
                             Default: 8080
      --gw-secure          Secure of the gateway service.
  -h, --help               Show this help message and exit.
      --home=<path>        Set the home directory.
                             Default: ../config
      --pretty             Pretty json print
      --ssl.ciphers=<ciphers>
                           Set ssl.ciphers for SSL.
      --ssl.enabled-protocols=<enabledProtocols>
                           Set ssl.enabled-protocols for SSL.
      --ssl.host-verifier=<hostNameVerifier>
                           Set host verifier for SSL. NO-OP or Default
                             Default: NO-OP
      --ssl.key-alias=<keyAlias>
                           Set ssl.key-alias for SSL.
      --ssl.key-store=<keyStore>
                           Set ssl.key-store for SSL.
      --ssl.key-store-password=<keyStorePassword>
                           Set ssl.key-store-password for SSL.
      --ssl.key-store-type=<keyStoreType>
                           Set ssl.key-store-type for SSL.
      --ssl.protocol=<protocol>
                           Set ssl.protocol for SSL.
      --ssl.trust-store=<trustStore>
                           Set ssl.trust-store for SSL.
      --ssl.trust-store-password=<trustStorePassword>
                           Set trust-store-password for SSL.
      --ssl.trust-store-type=<trustStoreType>
                           Set ssl.trust-store-type for SSL.
  -V, --version            Print version information and exit.
Commands:
  root-ca                   Update ledger root certificates.
  user-register             Register new user.
  user-ca                   Update user certificate.
  user-state                Update user(certificate) state.
  role                      Create or config role.
  authorization             User role authorization.
  data-account-register     Register new data account.
  data-account-permission   Update data account permission.
  kv                        Set key-value.
  event-account-register    Register event account.
  event-account-permission  Update event account permission.
  event                     Publish event.
  event-listen              Subscribe event.
  contract-deploy           Deploy or update contract.
  contract-permission       Update contract permission.
  contract                  Call contract method.
  contract-state            Update contract state.
  sign                      Sign transaction.
  send                      Send transaction.
  testkv                    Send kv set transaction for testing.
  consensus-switch          Switch consensus type.
  hash-algo-switch          Switch crypto hash algo.
  help                      Displays help information about the specified
                              command
```

参数：
- `export`，导出交易到指定位置，用于离线交易相关命令
- `gw-host`，网关服务地址，默认`127.0.0.1`
- `gw-port`，网关服务端口，默认`8080`
- `gw-secure`，网关是否启用安全连接
- `home`，指定配置（公私钥、证书等）目录，默认`../config`
- `ssl.key-alias`，`TLS`相关配置
- `ssl.key-store`，`TLS`相关配置
- `ssl.key-store-password`，`TLS`相关配置
- `ssl.key-store-type`，`TLS`相关配置
- `ssl.trust-store`，`TLS`相关配置
- `ssl.trust-store-password`，`TLS`相关配置
- `ssl.trust-store-type`，`TLS`相关配置

## 注册用户

```bash
:bin$ ./jdchain-cli.sh tx user-register -h
Register new user.
Usage: jdchain-cli tx user-register [-hV] [--ca-mode] [--pretty]
                                    [--crt=<caPath>] [--export=<export>]
                                    [--gw-host=<gwHost>] [--gw-port=<gwPort>]
                                    [--home=<path>] [-n=<name>]
                                    [--pubkey=<pubkey>]
      --crt=<caPath>       File of the X509 certificate
      --export=<export>    Transaction export directory
      --gw-host=<gwHost>   Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>   Set the gateway port. Default: 8080
  -h, --help               Show this help message and exit.
      --home=<path>        Set the home directory.
      --pretty             Pretty json print
      --pubkey=<pubkey>    Pubkey of the user
  -V, --version            Print version information and exit.
```
- `crt`，证书文件路径，证书身份模式下必填
- `pubkey`，`Base58`编码公钥信息

如：
```bash
:bin$ ./jdchain-cli.sh tx user-register --pubkey 7VeREEMD5aztb17PJwm6sVHJ7W6sZoEV5MsZ7AVJ4MTuLRsA
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
// 选择账本，当前网关服务只有上面一个可用账本
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
1      k1                                      LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC
// 选择链上已存在且有注册用户权限的用户所对应的公私钥对，用于交易签名
> 0
input password of the key:
// 输入签名私钥密码
> 1
register user: [LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC]
```
会在链上注册地址为`LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC`的用户账户信息。

## 更新用户证书

```bash
:bin$ ./jdchain-cli.sh tx user-ca -h
Update user certificate.
Usage: jdchain-cli tx user-ca [-hV] [--pretty] [--crt=<caPath>]
                                     [--export=<export>] [--gw-host=<gwHost>]
                                     [--gw-port=<gwPort>] [--home=<path>]
      --crt=<caPath>       File of the X509 certificate
      --export=<export>    Transaction export directory
      --gw-host=<gwHost>   Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>   Set the gateway port. Default: 8080
  -h, --help               Show this help message and exit.
      --home=<path>        Set the home directory.
      --pretty             Pretty json print
  -V, --version            Print version information and exit.
```
- `crt`，证书文件路径

如：
```bash
:bin$ $ ./jdchain-cli.sh tx user-ca --crt /home/imuge/jd/nodes/peer0/config/keys/peer0.crt
select ledger, input the index:
INDEX   LEDGER
0       j5pFrMigE47t6TobQJXsztnoeA29H31v1vHHF1wqCp4rzi
// 选择账本，当前网关服务只有上面一个可用账本
> 0
select keypair to sign tx:
INDEX   KEY     ADDRESS
0       peer0   LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W
// 选择链上已存在且有注册用户权限的用户所对应的公私钥对，用于交易签名
> 0
input password of the key:
// 输入签名私钥密码
> 1
user: [LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W] ca updated
```
会更新链上地址为`LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W`的用户证书信息。

## 更新用户(证书)状态

```bash
:bin$ ./jdchain-cli.sh tx user-state -h
Update user(certificate) state.
Usage: jdchain-cli tx user-state [-hV] [--pretty] --address=<address>
                                        [--export=<export>]
                                        [--gw-host=<gwHost>]
                                        [--gw-port=<gwPort>] [--home=<path>]
                                        --state=<state>
      --address=<address>   User address
      --export=<export>     Transaction export directory
      --gw-host=<gwHost>    Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>    Set the gateway port. Default: 8080
  -h, --help                Show this help message and exit.
      --home=<path>         Set the home directory.
      --pretty              Pretty json print
      --state=<state>       User state，Optional values: FREEZE,NORMAL,REVOKE
  -V, --version             Print version information and exit.
```
- `address`，用户地址
- `state`，用户状态，可选值：FREEZE，NORMAL，REVOKE

如冻结用户`LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W`：
```bash
:bin$ $ ./jdchain-cli.sh tx user-state --address LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W  --state FREEZE
select ledger, input the index:
INDEX   LEDGER
0       j5pFrMigE47t6TobQJXsztnoeA29H31v1vHHF1wqCp4rzi
// 选择账本，当前网关服务只有上面一个可用账本
> 0
select keypair to sign tx:
INDEX   KEY     ADDRESS
0       peer0   LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W
// 选择链上已存在且有注册用户权限的用户所对应的公私钥对，用于交易签名
> 0
input password of the key:
// 输入签名私钥密码
> 1
user: [LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W] revoked
```
会冻结链上地址为`LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W`的用户（证书），此用户无法再接入使用此网络。

## 角色管理
```bash
:bin$ ./jdchain-cli.sh tx role -h
Create or config role.
Usage: jdchain-cli tx role [-hV] [--pretty] [--export=<export>]
                           [--gw-host=<gwHost>] [--gw-port=<gwPort>]
                           [--home=<path>] --name=<role>
                           [--disable-ledger-perms=<disableLedgerPerms>[,
                           <disableLedgerPerms>...]]...
                           [--disable-transaction-perms=<disableTransactionPerms
                           >[,<disableTransactionPerms>...]]...
                           [--enable-ledger-perms=<enableLedgerPerms>[,
                           <enableLedgerPerms>...]]...
                           [--enable-transaction-perms=<enableTransactionPerms>
                           [,<enableTransactionPerms>...]]...
      --disable-ledger-perms=<disableLedgerPerms>[,<disableLedgerPerms>...]
                           Disable ledger permissions
      --disable-transaction-perms=<disableTransactionPerms>[,
        <disableTransactionPerms>...]
                           Disable transaction permissions
      --enable-ledger-perms=<enableLedgerPerms>[,<enableLedgerPerms>...]
                           Enable ledger permissions
      --enable-transaction-perms=<enableTransactionPerms>[,
        <enableTransactionPerms>...]
                           Enable transaction permissions
      --export=<export>    Transaction export directory
      --gw-host=<gwHost>   Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>   Set the gateway port. Default: 8080
  -h, --help               Show this help message and exit.
      --home=<path>        Set the home directory.
      --name=<role>        Role name
      --pretty             Pretty json print
  -V, --version            Print version information and exit.
```
- `name`，角色名称，不存在则创建
- `disable-ledger-perms`，禁用的账本权限列表，半角逗号分割
- `disable-transaction-perms`，禁用的交易权限列表，半角逗号分割
- `enable-ledger-perms`，的账本权限列表，半角逗号分割
- `enable-transaction-perms`，禁用的交易权限列表，半角逗号分割

如：
```bash
:bin$ ./jdchain-cli.sh tx role --name ROLE1 --enable-ledger-perms REGISTER_USER,REGISTER_DATA_ACCOUNT --enable-transaction-perms DIRECT_OPERATION,CONTRACT_OPERATION
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
// 选择账本
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
1      k1                                      LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC
// 选择签名账户
> 0
input password of the key:
// 输入签名账户私钥密码
> 1
Role config success!
```

## 权限配置

```bash
:bin$ ./jdchain-cli.sh tx authorization -h
User role authorization.
Usage: jdchain-cli tx authorization [-hV] [--pretty] --address=<address>
                                    [--export=<export>] [--gw-host=<gwHost>]
                                    [--gw-port=<gwPort>] [--home=<path>]
                                    [--policy=<policy>]
                                    [--authorize=<authorizeRoles>[,
                                    <authorizeRoles>...]]...
                                    [--unauthorize=<unauthorizeRoles>[,
                                    <unauthorizeRoles>...]]...
      --address=<address>   User address
      --authorize=<authorizeRoles>[,<authorizeRoles>...]
                            Authorize roles
      --export=<export>     Transaction export directory
      --gw-host=<gwHost>    Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>    Set the gateway port. Default: 8080
  -h, --help                Show this help message and exit.
      --home=<path>         Set the home directory.
      --policy=<policy>     Role policy
      --pretty              Pretty json print
      --unauthorize=<unauthorizeRoles>[,<unauthorizeRoles>...]
                            Unauthorize roles
  -V, --version             Print version information and exit.
```
- `address`，用户地址
- `authorize`，赋予角色列表，半角逗号分割
- `unauthorize`，移除角色列表，半角逗号分割
- `policy`，角色策略，`UNION`/`INTERSECT`，默认`UNION`合并所有角色权限

如：
```bash
:bin$ ./jdchain-cli.sh tx authorization --address LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC --authorize ROLE1
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
1      k1                                      LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC
> 0
input password of the key:
> 1
Authorization config success!
```

## 注册数据账户

```bash
:bin$ ./jdchain-cli.sh tx data-account-register -h
Register new data account.
Usage: jdchain-cli tx data-account-register [-hV] [--pretty]
       [--export=<export>] [--gw-host=<gwHost>] [--gw-port=<gwPort>]
       [--home=<path>] [--pubkey=<pubkey>]
      --export=<export>    Transaction export directory
      --gw-host=<gwHost>   Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>   Set the gateway port. Default: 8080
  -h, --help               Show this help message and exit.
      --home=<path>        Set the home directory.
      --pretty             Pretty json print
      --pubkey=<pubkey>    The pubkey of the exist data account
  -V, --version            Print version information and exit.
```
- `pubkey`，待注册数据账户公钥

如：
```bash
:bin$ ./jdchain-cli.sh tx data-account-register --pubkey 7VeRFk4ANQHjWjAmAoL7492fuykTpXujihJeAgbXT2J9H9Yk
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
1      k1                                      LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC
> 0
input password of the key:
> 1
register data account: [LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC]
```
会在链上注册地址为`LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC`的数据账户信息。

## 修改数据账户权限

```bash
:bin$ ./jdchain-cli.sh tx data-account-permission -h
Update data account permission.
Usage: jdchain-cli tx data-account-permission [-hV] [--pretty]
       [--address=<address>] [--export=<export>] [--gw-host=<gwHost>]
       [--gw-port=<gwPort>] [--home=<path>] [--mode=<mode>] [--role=<role>]
      --address=<address>   Address of the data account
      --export=<export>     Transaction export directory
      --gw-host=<gwHost>    Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>    Set the gateway port. Default: 8080
  -h, --help                Show this help message and exit.
      --home=<path>         Set the home directory.
      --mode=<mode>         Mode value of the data account
      --pretty              Pretty json print
      --role=<role>         Role of the data account
  -V, --version             Print version information and exit.
```
- `address`，数据账户地址
- `role`，数据账户所属角色
- `mode`，数据账户权限值

如：
```bash
:bin$ ./jdchain-cli.sh tx data-account-permission --address LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC --role ROLE1 --mode 777
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
> 0
input password of the key:
> 1
update data account: [LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC] permission
```
将修改数据账户`LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC`所属角色为`ROLE1`，权限值为`777`（所有用户可读可写）。

## KV设值

```bash
:bin$ ./jdchain-cli.sh tx kv -h
Set key-value.
Usage: jdchain-cli tx kv [-hV] [--pretty] --address=<address>
                         [--export=<export>] [--gw-host=<gwHost>]
                         [--gw-port=<gwPort>] [--home=<path>] --key=<key>
                         --value=<value> [--ver=<version>]
      --address=<address>   Data account address
      --export=<export>     Transaction export directory
      --gw-host=<gwHost>    Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>    Set the gateway port. Default: 8080
  -h, --help                Show this help message and exit.
      --home=<path>         Set the home directory.
      --key=<key>           Key to set
      --pretty              Pretty json print
  -V, --version             Print version information and exit.
      --value=<value>       Value to set
      --ver=<version>       Version of the key-value
```
- `address`，数据账户地址
- `key`，键
- `value`，值
- `ver`，版本

如向账户地址`LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC`写入`k1`:`v1`:`-1`键值对数据：
```bash
:bin$ ./jdchain-cli.sh tx kv --address LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC --key k1 --value v1 --ver -1
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
1      k1                                      LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC
> 0
input password of the key:
> 1
set kv success
```
## 切换共识算法

```bash
:bin$ ./jdchain-cli.sh tx consensus-switch -h
Switch consensus type.
Usage: jdchain-cli tx consensus-switch [-hV] [--gw-secure] [--pretty]
                                       --config=<config>
                                       --consensus=<consensus>
                                       [--export=<export>] [--gw-host=<gwHost>]
                                       [--gw-port=<gwPort>] [--home=<path>]
                                       [--ssl.ciphers=<ciphers>] [--ssl.
                                       enabled-protocols=<enabledProtocols>]
                                       [--ssl.host-verifier=<hostNameVerifier>]
                                       [--ssl.key-alias=<keyAlias>] [--ssl.
                                       key-store=<keyStore>] [--ssl.
                                       key-store-password=<keyStorePassword>]
                                       [--ssl.key-store-type=<keyStoreType>]
                                       [--ssl.protocol=<protocol>] [--ssl.
                                       trust-store=<trustStore>] [--ssl.
                                       trust-store-password=<trustStorePassword>
                                       ] [--ssl.
                                       trust-store-type=<trustStoreType>]
      --config=<config>    Set new consensus config file
      --consensus=<consensus>
                           New consensus type. Options: `BFTSMART`,`RAFT`,`MQ`
  -h, --help               Show this help message and exit.
  -V, --version            Print version information and exit.
```
- `config`，目标共识类型的共识配置文件必填
- `consensus`，目标共识类型必填

如：
```bash
:bin$ ./jdchain-cli.sh tx consensus-switch --consensus RAFT --config ../config/init/raft/raft.config 
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
// 选择账本，当前网关服务只有上面一个可用账本
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
1      k1                                      LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC
// 选择链上已存在且有注册用户权限的用户所对应的公私钥对，用于交易签名
> 0
input password of the key:
// 输入签名私钥密码
> 1
switch consensus type success
```
会在链上更新共识类型以及共识配置信息。

## 切换哈希算法

```bash
:bin$ ./jdchain-cli.sh tx hash-algo-switch -h
Switch crypto hash algo.
Usage: jdchain-cli tx hash-algo-switch [-hV] [--gw-secure] [--pretty]
                                       -a=<newHashAlgo> [--export=<export>]
                                       [--gw-host=<gwHost>]
                                       [--gw-port=<gwPort>] [--home=<path>]
                                       [--ssl.ciphers=<ciphers>] [--ssl.
                                       enabled-protocols=<enabledProtocols>]
                                       [--ssl.host-verifier=<hostNameVerifier>]
                                       [--ssl.key-alias=<keyAlias>] [--ssl.
                                       key-store=<keyStore>] [--ssl.
                                       key-store-password=<keyStorePassword>]
                                       [--ssl.key-store-type=<keyStoreType>]
                                       [--ssl.protocol=<protocol>] [--ssl.
                                       trust-store=<trustStore>] [--ssl.
                                       trust-store-password=<trustStorePassword>
                                       ] [--ssl.
                                       trust-store-type=<trustStoreType>]
  -a, --algorithm=<newHashAlgo>
                           New crypto hash algo.
                             Options:'SHA256','RIPEMD160','SM3'
  -V, --version            Print version information and exit.
```
- `algorithm`，目标哈希算法必填

如：
```bash
:bin$ ./jdchain-cli.sh tx hash-algo-switch --algorithm SM3
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
// 选择账本，当前网关服务只有上面一个可用账本
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
1      k1                                      LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC
// 选择链上已存在且有注册用户权限的用户所对应的公私钥对，用于交易签名
> 0
input password of the key:
// 输入签名私钥密码
> 1
switch new hash algo success
```
会在链上更新账本配置信息中的哈希算法。

## 注册事件账户

```bash
:bin$ ./jdchain-cli.sh tx event-account-register -h
Register event account.
Usage: jdchain-cli tx event-account-register [-hV] [--pretty]
       [--export=<export>] [--gw-host=<gwHost>] [--gw-port=<gwPort>]
       [--home=<path>] [--pubkey=<pubkey>]
      --export=<export>    Transaction export directory
      --gw-host=<gwHost>   Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>   Set the gateway port. Default: 8080
  -h, --help               Show this help message and exit.
      --home=<path>        Set the home directory.
      --pretty             Pretty json print
      --pubkey=<pubkey>    The pubkey of the exist event account
  -V, --version            Print version information and exit.
```
- `pubkey`，待注册事件账户公钥

如：
```bash
:bin$ ./jdchain-cli.sh tx event-account-register --pubkey 7VeRFk4ANQHjWjAmAoL7492fuykTpXujihJeAgbXT2J9H9Yk
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
1      k1                                      LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC
> 0
input password of the key:
> 1
register event account: [LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC]
```
会在链上注册地址为`LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC`的事件账户信息。

## 修改事件账户权限

```bash
:bin$ ./jdchain-cli.sh tx event-account-permission -h
Update event account permission.
Usage: jdchain-cli tx event-account-permission [-hV] [--pretty]
       [--address=<address>] [--export=<export>] [--gw-host=<gwHost>]
       [--gw-port=<gwPort>] [--home=<path>] [--mode=<mode>] [--role=<role>]
      --address=<address>   Address of the event account
      --export=<export>     Transaction export directory
      --gw-host=<gwHost>    Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>    Set the gateway port. Default: 8080
  -h, --help                Show this help message and exit.
      --home=<path>         Set the home directory.
      --mode=<mode>         Mode value of the event account
      --pretty              Pretty json print
      --role=<role>         Role of the event account
  -V, --version             Print version information and exit.
```
- `address`，事件账户地址
- `role`，事件账户所属角色
- `mode`，事件账户权限值

如：
```bash
:bin$ ./jdchain-cli.sh tx event-account-permission --address LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC --role ROLE1 --mode 777
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
> 0
input password of the key:
> 1
update event account: [LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC] permission
```
将修改事件账户`LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC`所属角色为`ROLE1`，权限值为`777`（所有用户可读可写）。

## 发布事件
```bash
:bin$ ./jdchain-cli.sh tx event -h
Publish event.
Usage: jdchain-cli tx event [-hV] [--pretty] --address=<address>
                            --content=<value> [--export=<export>]
                            [--gw-host=<gwHost>] [--gw-port=<gwPort>]
                            [--home=<path>] [--sequence=<sequence>]
                            --name=<name>
      --address=<address>   Contract address
      --content=<value>     Event content
      --export=<export>     Transaction export directory
      --gw-host=<gwHost>    Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>    Set the gateway port. Default: 8080
  -h, --help                Show this help message and exit.
      --home=<path>         Set the home directory.
      --pretty              Pretty json print
      --sequence=<sequence> Sequence of the event
      --name=<name>         Event name
  -V, --version             Print version information and exit.
```
- `address`，事件账户地址
- `name`，事件名
- `content`，事件内容
- `sequence`，事件序号

如向账户地址`LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC`发布事件`n1`:`c1`:`-1`：
```bash
:bin$ ./jdchain-cli.sh tx event --address LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC --name n1 --content c1 --sequence -1
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
1      k1                                      LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC
> 0
input password of the key:
> 1
event publish success
```

## 监听事件
```bash
:bin$ ./jdchain-cli.sh tx event-listen -h
Subscribe event.
Usage: jdchain-cli tx event-listen [-hV] [--pretty] [--address=<address>]
                                   [--export=<export>] [--gw-host=<gwHost>]
                                   [--gw-port=<gwPort>] [--home=<path>]
                                   --name=<name> [--sequence=<sequence>]
      --address=<address>   Event address
      --export=<export>     Transaction export directory
      --gw-host=<gwHost>    Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>    Set the gateway port. Default: 8080
  -h, --help                Show this help message and exit.
      --home=<path>         Set the home directory.
      --name=<name>         Event name
      --pretty              Pretty json print
      --sequence=<sequence> Sequence of the event
  -V, --version             Print version information and exit.
```
- `address`，事件账户地址，不传则表示监听系统事件
- `name`，事件名，系统事件目前仅支持：`new_block_created`
- `sequence`，起始监听序号

如监听系统新区块事件：
```bash
:bin$ ./jdchain-cli.sh tx event-listen --name new_block_created --sequence 0
select ledger, input the index:
INDEX   LEDGER
0       j5mXXoNsmh6qadnWLjxFMXobyNGsXT1PmTNzXiHyiYMxoP
> 0
# 会打印新区块事件：区块高度：最新区块高度
New block:0:12
New block:1:12
New block:2:12
```

## 部署合约

```bash
:bin$ ./jdchain-cli.sh tx contract-deploy -h
Deploy or update contract.
Usage: jdchain-cli tx contract-deploy [-hV] [--gw-secure] [--pretty]
                                      --code=<code> [--export=<export>]
                                      [--gw-host=<gwHost>] [--gw-port=<gwPort>]
                                      [--home=<path>] [--lang=<lang>]
                                      [--pubkey=<pubkey>] [--ssl.
                                      ciphers=<ciphers>] [--ssl.
                                      enabled-protocols=<enabledProtocols>]
                                      [--ssl.host-verifier=<hostNameVerifier>]
                                      [--ssl.key-alias=<keyAlias>] [--ssl.
                                      key-store=<keyStore>] [--ssl.
                                      key-store-password=<keyStorePassword>]
                                      [--ssl.key-store-type=<keyStoreType>]
                                      [--ssl.protocol=<protocol>] [--ssl.
                                      trust-store=<trustStore>] [--ssl.
                                      trust-store-password=<trustStorePassword>]
                                       [--ssl.trust-store-type=<trustStoreType>]
      --code=<code>        The contract file file path
  -h, --help               Show this help message and exit.
      --home=<path>        Set the home directory.
      --lang=<lang>        The contract language
      --pretty             Pretty json print
      --pubkey=<pubkey>    The pubkey of the exist contract
  -V, --version            Print version information and exit.
```
- `pubkey`，合约公钥，更新合约时使用
- `code`，合约文件
- `lang`，合约语言，目前支持：`Java`、`JavaScript`、`Python`，默认`Java`

如将`contract-samples-1.5.0.RELEASE.car`文件中的合约部署上链：
```bash
:bin$ ./jdchain-cli.sh tx contract-deploy --code /home/imuge/Desktop/jdchain-cli/1.5.0/contract-samples-1.5.0.RELEASE.car
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
1      k1                                      LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC
> 0
input password of the key:
> 1
deploy contract: [LdeNyF6jdNry5iCqmHdAFTQPvC8UkbJ9avoXH]
```
合约地址：`LdeNyF6jdNry5iCqmHdAFTQPvC8UkbJ9avoXH`

## 修改合约权限

```bash
:bin$ ./jdchain-cli.sh tx contract-permission -h
Update contract permission.
Usage: jdchain-cli tx contract-permission [-hV] [--pretty]
       [--address=<address>] [--export=<export>] [--gw-host=<gwHost>]
       [--gw-port=<gwPort>] [--home=<path>] [--mode=<mode>] [--role=<role>]
      --address=<address>   Address of the contract
      --export=<export>     Transaction export directory
      --gw-host=<gwHost>    Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>    Set the gateway port. Default: 8080
  -h, --help                Show this help message and exit.
      --home=<path>         Set the home directory.
      --mode=<mode>         Mode value of the contract
      --pretty              Pretty json print
      --role=<role>         Role of the contract
  -V, --version             Print version information and exit.
```
- `address`，合约地址
- `role`，合约所属角色
- `mode`，合约权限值

如：
```bash
:bin$ ./jdchain-cli.sh tx contract-permission --address LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC --role ROLE1 --mode 777
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
> 0
input password of the key:
> 1
update contract: [LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC] permission
```
将修改合约`LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC`所属角色为`ROLE1`，权限值为`777`（所有用户可读可写）。

## 合约调用

```bash
:bin$ ./jdchain-cli.sh tx contract -h
Call contract method.
Usage: jdchain-cli tx contract [-hV] [--pretty] --address=<address>
                               [--export=<export>] [--gw-host=<gwHost>]
                               [--gw-port=<gwPort>] [--home=<path>]
                               --method=<method> [--args=<args>[,<args>...]]...
      --address=<address>   Contract address
      --args=<args>[,<args>...]
                            Method arguments
      --export=<export>     Transaction export directory
      --gw-host=<gwHost>    Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>    Set the gateway port. Default: 8080
  -h, --help                Show this help message and exit.
      --home=<path>         Set the home directory.
      --method=<method>     Contract method
      --pretty              Pretty json print
  -V, --version             Print version information and exit.
```
- `address`，合约地址
- `method`，合约方法
- `args`，合约参数，半角逗号分割，命令行中会将所有参数处理为字符串，非字符串参数方法调用暂无法通过命令行工具调用

如调用合约`LdeNyF6jdNry5iCqmHdAFTQPvC8UkbJ9avoXH`中`registerUser`方法，传参`ed386a148fcb48b281b325f66103c805`：
```bash
:bin$ ./jdchain-cli.sh tx contract --address LdeNyF6jdNry5iCqmHdAFTQPvC8UkbJ9avoXH --method registerUser --args ed386a148fcb48b281b325f66103c805
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
> 0
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
1      k1                                      LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC
> 0
input password of the key:
> 1
call contract success
return string: LdeNqvSjL4izfpMNsGpQiBpTBse4g6qLxZ6j5
```
调用成功并返回了字符串：`LdeNqvSjL4izfpMNsGpQiBpTBse4g6qLxZ6j5`

## 更新合约状态

```bash
:bin$ ./jdchain-cli.sh tx contract-state -h
Update contract state.
Usage: jdchain-cli tx contract-state [-hV] [--pretty]
       --address=<address> [--export=<export>] [--gw-host=<gwHost>]
       [--gw-port=<gwPort>] [--home=<path>] --state=<state>
      --address=<address>   Contract address
      --export=<export>     Transaction export directory
      --gw-host=<gwHost>    Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>    Set the gateway port. Default: 8080
  -h, --help                Show this help message and exit.
      --home=<path>         Set the home directory.
      --pretty              Pretty json print
      --state=<state>       Contract state，Optional values: FREEZE,NORMAL,
                              REVOKE
  -V, --version             Print version information and exit.
```
- `address`，合约地址
- `state`，合约状态，可选值：FREEZE，NORMAL，REVOKE

如冻结合约`LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W`：
```bash
:bin$ $ ./jdchain-cli.sh tx contract-state --address LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W  --state FREEZE
select ledger, input the index:
INDEX   LEDGER
0       j5pFrMigE47t6TobQJXsztnoeA29H31v1vHHF1wqCp4rzi
// 选择账本，当前网关服务只有上面一个可用账本
> 0
select keypair to sign tx:
INDEX   KEY     ADDRESS
0       peer0   LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W
// 选择链上已存在且有注册用户权限的用户所对应的公私钥对，用于交易签名
> 0
input password of the key:
// 输入签名私钥密码
> 1
contract: [LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W] revoked
```
会冻结链上地址为`LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W`的合约，此合约不能再被调用。

## 更新账本根证书

```bash
:bin$ ./jdchain-cli.sh tx root-ca -h
Update ledger root certificates.
Usage: jdchain-cli tx ledger-ca-update [-hV] [--pretty] --crt=<caPath>
                                       [--export=<export>] [--gw-host=<gwHost>]
                                       [--gw-port=<gwPort>] [--home=<path>]
      --crt=<caPath>       File of the X509 certificate
      --operation          Operation for this certificate. Optional values: ADD,UPDATE,REMOVE
      --export=<export>    Transaction export directory
      --gw-host=<gwHost>   Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>   Set the gateway port. Default: 8080
  -h, --help               Show this help message and exit.
      --home=<path>        Set the home directory.
      --pretty             Pretty json print
  -V, --version            Print version information and exit.
```
- `crt`，证书文件路径
- `operation`，操作类型：`ADD`，`UPDATE`,`REMOVE`

如：
```bash
:bin$ $ ./jdchain-cli.sh tx root-ca --crt /home/imuge/jd/nodes/peer0/config/keys/ledger.crt --operation UPDATE
select ledger, input the index:
INDEX   LEDGER
0       j5pFrMigE47t6TobQJXsztnoeA29H31v1vHHF1wqCp4rzi
// 选择账本，当前网关服务只有上面一个可用账本
> 0
select keypair to sign tx:
INDEX   KEY     ADDRESS
0       peer0   LdeNpEmyh5DMwbAwamxNaiJgMVGn6aTtQDA5W
// 选择链上已存在且有注册用户权限的用户所对应的公私钥对，用于交易签名
> 0
input password of the key:
// 输入签名私钥密码
> 1
ledger ca: [7VeRBQ9jpsgNXje2NYXU5MhyGKVRj462RtkJ8f6FNL1oxYbX](pubkey) updated
```
会更新链上公钥为`7VeRBQ9jpsgNXje2NYXU5MhyGKVRj462RtkJ8f6FNL1oxYbX`的账本证书信息。

## 离线交易签名

1. 离线交易

执行以上所有交易时，若`export`参数不为空，则会执行交易写入本地操作，而非签名并发送交易

如构造合约调用操作交易并保存到本地：
```bash
:bin$ ./jdchain-cli.sh tx contract --address LdeNyF6jdNry5iCqmHdAFTQPvC8UkbJ9avoXH --method registerUser --args ed386a148fcb48b281b325f66103c810 --export /txs
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
> 0
export transaction success: /txs/j5xR8ty8YbujTYKNRshmbfMYsL4jfe3yRUtMparmeHppd3
```
交易内容会被序列化保存在`/txs/j5xR8ty8YbujTYKNRshmbfMYsL4jfe3yRUtMparmeHppd3`中，其中`j5xR8ty8YbujTYKNRshmbfMYsL4jfe3yRUtMparmeHppd3`是该交易哈希。

2. 离线签名

```bash
:bin$ ./jdchain-cli.sh tx sign -h
Sign transaction.
Usage: jdchain-cli tx sign [-hV] [--pretty] [--export=<export>]
                           [--gw-host=<gwHost>] [--gw-port=<gwPort>]
                           [--home=<path>] [--tx=<txFile>]
      --export=<export>    Transaction export directory
      --gw-host=<gwHost>   Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>   Set the gateway port. Default: 8080
  -h, --help               Show this help message and exit.
      --home=<path>        Set the home directory.
      --tx=<txFile>        Local transaction file
      --pretty             Pretty json print
  -V, --version            Print version information and exit.
```
- `tx`，离线交易路径

如对步骤1中创建的离线交易添加终端用户（此用户需是链上存在的且有相关权限的用户）签名：
```bash
:bin$ ./jdchain-cli.sh tx sign --tx /txs/j5xR8ty8YbujTYKNRshmbfMYsL4jfe3yRUtMparmeHppd3
select keypair to sign tx:
INDEX  KEY                                     ADDRESS
0      peer0                                   LdeNyibeafrAQXgHjBxgQxoLbna6hL4BcXZiw
1      k1                                      LdeNwQWabrf6WSjZ35saFo52MfQFhVKvm11aC
> 0
input password of the key:
> 1
Sign transaction success!
```

## 离线交易发送

发送本地已经签名完成的交易
```bash
:bin$ ./jdchain-cli.sh tx send -h
Send transaction.
Usage: jdchain-cli tx send [-hV] [--pretty] [--export=<export>]
                           [--gw-host=<gwHost>] [--gw-port=<gwPort>]
                           [--home=<path>] [--tx=<txFile>]
      --export=<export>    Transaction export directory
      --gw-host=<gwHost>   Set the gateway host. Default: 127.0.0.1
      --gw-port=<gwPort>   Set the gateway port. Default: 8080
  -h, --help               Show this help message and exit.
      --home=<path>        Set the home directory.
      --tx=<txFile>		   Local transaction file
      --pretty             Pretty json print
  -V, --version            Print version information and exit.
```
- `tx`，离线交易路径

如发送`/txs/j5xR8ty8YbujTYKNRshmbfMYsL4jfe3yRUtMparmeHppd3`中包含的交易数据：
```bash
:bin$ ./jdchain-cli.sh tx send --tx /home/imuge/Desktop/jdchain-cli/1.5.0/txs/j5xR8ty8YbujTYKNRshmbfMYsL4jfe3yRUtMparmeHppd3
select ledger, input the index:
INDEX  LEDGER
0      j5sB3sVTFgTqTYzo7KtQjBLSy8YQGPpJpvQZaW9Eqk46dg
> 0
Send transaction success: j5xR8ty8YbujTYKNRshmbfMYsL4jfe3yRUtMparmeHppd3
```

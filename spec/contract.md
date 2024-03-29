JD Chain 账本中以合约账户的方式对合约代码进行管理。一份部署上链的合约代码需要关联到一个唯一的公钥上，并生成与公钥对应的区块链账户地址，在账本中注册为一个合约账户。在执行之前，系统从账本中读出合约代码并将其加载到合约引擎，由交易执行器调用合约引擎触发合约执行。

JD Chain 账本定义了一组标准的账本操作指令，合约代码的执行过程实质上是向账本输出一串操作指令序列，这些指令对账本中的数据产生了变更，形成合约执行的最终结果。

JD Chain 支持多语言合约实现，`1.6.4`版本已实现：`Java`、`JavaScript`、`Python`、`Rust`四种合约语言。

## Java Contract

### 准备开发环境

按照正常的 Java 应用开发环境要求进行准备，以 Maven 作为代码工程的构建管理工具，无其它特殊要求。

>检查 JDK 版本不低于 1.8 ，Maven 版本不低于 3.0。

### 创建合约代码工程
创建一个普通的 Java Maven 工程，打开 pom.xml 把 packaging 设为 contract .

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>your.group.id</groupId>
  <artifactId>your.project</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <!-- 声明为合约代码工程，编译输出扩展名为".car"合约代码 -->
  <packaging>contract</packaging>

  <dependencies>
     <!-- 合约项目的依赖 -->
  </dependencies>

  <build>
     <plugins>
        <!-- 合约项目的插件 -->
     </plugins>
  </build>
</project>
```

> 注：合约代码工程也是一个普通的 Java Maven 工程，因此尽管不同 IDE 创建 Maven 工程有不同的操作方式，由于对于合约开发而言并无特殊要求，故在此不做详述。

### 加入合约开发依赖

在合约代码工程 pom.xml 加入对合约开发 SDK 的依赖：

 ```xml
<dependency>
    <groupId>com.jd.blockchain</groupId>
    <artifactId>contract-starter</artifactId>
    <version>${jdchain.version}</version>
</dependency>
 ```

### 加入合约插件

在合约代码工程的 pom.xml 加入 contract-maven-plugin 插件：
 ```xml
<plugin>
   <groupId>com.jd.blockchain</groupId>
   <artifactId>contract-maven-plugin</artifactId>
   <version>${jdchain.version}</version>
   <extensions>true</extensions>
</plugin>
 ```

完整的 pom.xml 如下：

 ```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>your.group.id</groupId>
  <artifactId>your.project</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <!-- 声明为合约代码工程，编译输出扩展名为".car"合约代码 -->
  <packaging>contract</packaging>

  <properties>
     <jdchain.version>1.6.0.RELEASE</jdchain.version>
  </properties>
  
  <dependencies>
     <!-- 合约项目的依赖 -->
     <dependency>
        <groupId>com.jd.blockchain</groupId>
        <artifactId>contract-starter</artifactId>
        <version>${jdchain.version}</version>
     </dependency>
  </dependencies>

  <build>
     <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <encoding>UTF-8</encoding>
                <optimize>false</optimize>
                <debug>true</debug>
                <showDeprecation>false</showDeprecation>
                <showWarnings>false</showWarnings>
            </configuration>
        </plugin>

        <!-- 合约项目的插件 -->
        <plugin>
           <groupId>com.jd.blockchain</groupId>
           <artifactId>contract-maven-plugin</artifactId>
           <version>${jdchain.version}</version>
           <extensions>true</extensions>
        </plugin>
     </plugins>
  </build>
</project>
 ```

### 编写合约代码

> **注意事项**
> 1. 不允许合约（包括合约接口和合约实现类）使用com.jd.blockchain开头的package；
> 2. 必须有且只有一个接口使用@Contract注解，且其中的event必须大于等于一个；
> 3. 使用@Contract注解的接口有且只有一个实现类；
> 4. 黑名单调用限制（具体黑名单可查看配置文件），需要注意的是，黑名单分析策略会递归分析类实现的接口和父类，也就是说调用一个实现了指定黑名单接口的类也是不允许的；


1. **黑名单**
```conf
java.io.File
java.io.InputStream
java.io.OutputStream
java.io.DataInput
java.io.DataOutput
java.io.Reader
java.io.Writer
java.io.Flushable
java.nio.channels.*
java.nio.file.*
java.net.*
java.sql.*
java.lang.reflect.*
java.lang.Class
java.lang.ClassLoader
java.util.Random
java.lang.System-currentTimeMillis
java.lang.System-nanoTime
com.jd.blockchain.ledger.BlockchainKeyGenerator
java.lang.Thread
```

2. **声明合约**

```java
/**
 * 声明合约接口；
**/ 
@Contract
public interface AssetContract {

   @ContractEvent(name = "transfer")
   String transfer(String address, String from, String to, long amount);
   
}
```

3. **实现合约**

```java
/**
 * 实现合约；
 * 
 * 实现 EventProcessingAware 接口是可选的，目的获得 ContractEventContext 上下文对象，
 * 通过该对象可以进行账本操作；
 */
public class AssetContractImpl implements AssetContract, EventProcessingAware {
   
   // 合约事件上下文；
   private ContractEventContext eventContext;

   /**
    * 执行交易请求中对 AssetContract 合约的 transfer 调用操作；
    */
   public String transfer(String address, String from, String to, long amount) {
      //当前账本的哈希；
      HashDigest ledgerHash = eventContext.getCurrentLedgerHash();
      //当前账本上下文；
      LedgerContext ledgerContext = eventContext.getLedger();

      //做操作；
      // ledgerContext.

      //返回合约操作的结果；
      return "success";
   }

   /**
    * 准备执行交易中的合约调用操作；
    */
   @Override
   public void beforeEvent(ContractEventContext eventContext) {
      this.eventContext = eventContext;
   }

   /**
    * 完成执行交易中的合约调用操作；
    */
   @Override
   public void postEvent(ContractEventContext eventContext, Exception error) {
      this.eventContext = null;
   }
}
```

5. **账本数据可见范围**：

`ContractEventContext`中`getUncommittedLedger`方法可访问执行中的未提交区块数据，此方法的合理使用可以解决客户并发调用合约方法涉及数据版本/事件序列冲突的问题。

```java
/**
 * 当前包含未提交区块数据账本查询上下文；
 */
LedgerQueryService getUncommittedLedger();
```

`ContractEventContext`中`getLedger`方法访问的是链上已提交的最新区块数据，不包含未提交交易，所以存在未提交交易中多个合约方法调用操作间数据不可见，导致并发时数据版本等冲突问题。
```java
/**
 * 账本操作上下文；
 */
LedgerContext getLedger();
```

合约方法中对账本的操作通过调用`LedgerContext`中相关方法，可参照[示例合约](https://github.com/blockchain-jd-com/jdchain-samples/tree/master/contract-samples/src/main/java/com/jdchain/samples/contract)

### 编译打包合约代码

合约代码工程的编译打包操作与普通的 maven 工程是相同的，在工程的根目录下输入以下命令：

```bash
mvn clean package
```

执行成功之后，在 target 目录中输出合约代码文件 \<project-name>.\<version>.car 。

> 注意：合约代码虽然利用了 Java 语言，遵照 Java 语法进行编写，但本质上是作为一种运行于受限环境（合约虚拟机）的语言来使用，因而一些 Java 语法和 SDK 的 API 是不被允许使用的，在编译过程中将对此进行检查。

### 部署合约代码

1. 在项目中部署合约代码

如果希望在构建打包的同时将合约代码部署到指定的区块链网络，可以在合约代码工程 pom.xml 的 contract-maven-plugin 插件配置中加入合约部署相关的信息（具体更详细的配置可以参考“3. 合约插件详细配置”）。

```xml
<plugin>
   <groupId>com.jd.blockchain</groupId>
   <artifactId>contract-maven-plugin</artifactId>
   <version>1.2.0.RELEASE</version>
   <extensions>true</extensions>
   <configuration>
      <!-- 合约部署配置 -->
      <deployment>
         <!-- 合约要部署的目标账本的哈希；Base58 格式； -->
         <ledger>j5rpuGWVxSuUbU3gK7MDREfui797AjfdHzvAMiSaSzydu7</ledger>

         <!-- 区块链网络的网关地址 -->
         <gateway>
            <host>192.168.10.10</host>
            <port>8081</port>
         </gateway>

         <!-- 合约账户 -->
         <ContractAddress>
             <pubKey>7VeRMpXVeTY4cqPogUHeNoZNk86CGAejBh9Xbd5ndFZXNFj3</pubKey>
         </ContractAddress>

         <!-- 合约部署交易的签名账户；该账户必须具备合约部署的权限； -->
         <signer>
            <pubKey>7VeRLdGtSz1Y91gjLTqEdnkotzUfaAqdap3xw6fQ1yKHkvVq</pubKey>
            <privKey>177gjzHTznYdPgWqZrH43W3yp37onm74wYXT4v9FukpCHBrhRysBBZh7Pzdo5AMRyQGJD7x</privKey>
            <privKeyPwd>DYu3G8aGTMBW1WrTw76zxQJQU4DHLw9MLyy7peG4LKkY</privKeyPwd>
         </signer>
      </deployment>
   </configuration>
</plugin>
```

加入部署配置信息之后，对工程执行编译打包操作，输出的合约代码（.car）将自动部署到指定的区块链网络。

```bash
mvn clean deploy
```
2. 发布已编译好的car

使用[jdchain-cli](cli/tx#部署合约)或者[SDK](#合约部署)部署合约

## GraalVM Contract

JD Chain非JVM语言合约将基于[GraalVM](https://www.graalvm.org/)实现。

### GraalVM

GraalVM 是一个高性能 JDK 发行版，旨在加速用 Java 和其他 JVM 语言编写的应用程序的执行，同时支持 JavaScript、Ruby、Python 和许多其他流行语言。GraalVM 的多语言功能可以在单个应用程序中混合多种编程语言，同时消除外语调用成本。

GraalVM Polyglot API 允许在基于 JVM 的主机应用程序中嵌入和运行来自其他类型语言的代码，直接与这些语言互操作，并在同一内存空间中来回传递数据。

### 合约语言

基于GraalVM JD Chain 非JVM语言已实现`JavaScript`、`Python`。

### 合约设计

#### 合约结构

`ContractInfo`增加`合约语言`字段：

```java
@DataContract(code= DataCodes.CONTRACT_ACCOUNT_HEADER)
public interface ContractInfo extends BlockchainIdentity, AccountSnapshot, PermissionAccount {

    @DataField(order=4, primitiveType= PrimitiveType.BYTES)
    byte[] getChainCode();

    @DataField(order=5, primitiveType= PrimitiveType.INT64)
    long getChainCodeVersion();

    @DataField(order=6, refEnum = true)
    ContractLang getLang();
}
```

`ContractLang`：

```java
/**
 * 合约语言
 */
@EnumContract(code = DataCodes.CONTRACT_LANG)
public enum ContractLang {

    Java((byte) 0x01),
    JavaScript((byte) 0x02),
    Python((byte) 0x03);

    @EnumField(type = PrimitiveType.INT8)
    public final byte CODE;

    ContractLang(byte code) {
        this.CODE = code;
    }

}
```

#### 合约规范

区别于Java合约使用car包方式，非JVM语言合约将使用源码/位码（LLVM）在链上链下交互。

基于官方提供对应语言的链上数据交互接口或依赖，这些依赖提供链上数据访问、写入的封装，用户合约方法可自由组装这些接口实现复杂业务逻辑。

##### JavaScript

`JavaScript`语言合约执行前会自动注入：

- `eventContext`，合约执行上下文，提供账本数据库查询、写入能力
- `JSONUtils`，JSON序列化，JD Chain账本数据库部分数据结构序列化存在特殊处理，在序列化JD Chain定义的数据结构时请使用`JSONUtils.stringify(***)`
- `LOGGER`，`log4j2`日志组件，如`info`级别日志输出：`LOGGER.info()`

以下`JS`方法提供了KV写入、数据版本查询、数据查询基本功能，可直接保存当做合约文件使用，也可以自由组合创建复杂的合约逻辑。
> 以下方法直接作为合约方法调用时，所有参数均不能是`undefined`
```js
// 写入KV, 内容为字符串，使用最新版本
function setText(address, key, value) {
    version = getVersion(address, key);
    setTextWithVersion(address, key, value, version)
}

// 写入KV, 内容为字符串，版本参数传入
function setTextWithVersion(address, key, value, version) {
    eventContext.getLedger().dataAccount(address).setText(key, value, version);
}

// 写入KV, 内容为long，使用最新版本
function setInt64(address, key, value) {
    version = getVersion(address, key);
    setInt64WithVersion(address, key, value, version)
}

// 写入KV, 内容为long，版本参数传入
function setInt64WithVersion(address, key, value, version) {
    eventContext.getLedger().dataAccount(address).setInt64(key, value, version);
}

// 获取数据最新版本
function getVersion(address, key) {
    var dataEntries = eventContext.getUncommittedLedger().getDataEntries(address, key);
    if (dataEntries != null && dataEntries.length > 0) {
        return dataEntries[0].getVersion();
    } else {
        return -1;
    }
}

// 获取最新版本数据值
function getValue(address, key) {
    version = getVersion(address, key);
    return getValueWithVersion(address, key, version);
}

// 获取指定版本数据值
function getValueWithVersion(address, key, version) {
    if (version == -1) {
        return null;
    }
    var dataEntry = eventContext.getUncommittedLedger().getDataEntry(address, key, version);
    if (null != dataEntry) {
        return dataEntry.getValue();
    } else {
        return null;
    }
}

// 合约方法执行前操作
function beforeEvent(eventContext) {

}

// 合约方法执行后操作
function postEvent(eventContext, error) {

}
```

> 合约方法参数限制：仅可使用 `String`、`int`、`long`、`boolean`、`byte[]`
>
> 合约方法返回值限制：仅可使用 `String`、`int`、`long`、`boolean`、`byte[]`

可以直接复制以上js源码，保存为`xxx.js`文件，使用命令行部署示例：
```bash
./jdchain-cli.sh tx contract-deploy --code xxx.js --lang JavaScript
```

SDK部署时使用带合约语言的`deploy()`重载方法，调用方式与Java合约无异。

##### Python

**运行在GraalVM JDK下的JD Chain才可以调用Python语言合约**

和`JavaScript`合约一样，`Python`语言合约执行前会自动注入：

- `eventContext`，合约执行上下文，提供账本数据库查询、写入能力
- `JSONUtils`，JSON序列化，JD Chain账本数据库部分数据结构序列化存在特殊处理，在序列化JD Chain定义的数据结构时请使用`JSONUtils.stringify(***)`
- `LOGGER`，`log4j2`日志组件，如`info`级别日志输出：`LOGGER.info()`

以下`python`方法提供了KV写入、数据版本查询、数据查询基本功能，可直接保存当做合约文件使用，也可以自由组合创建复杂的合约逻辑。
> 以下方法直接作为合约方法调用时，所有参数均不能是`None`

```python
# 写入KV, 内容为字符串，使用最新版本
def setText(address, key, value):
    version = getVersion(address, key)
    setTextWithVersion(address, key, value, version)


# 写入KV, 内容为字符串，版本参数传入
def setTextWithVersion(address, key, value, version):
    eventContext.getLedger().dataAccount(address).setText(key, value, version)


# 写入KV, 内容为long，使用最新版本
def setInt64(address, key, value):
    version = getVersion(address, key)
    setInt64WithVersion(address, key, value, version)


# 写入KV, 内容为long，版本参数传入
def setInt64WithVersion(address, key, value):
    version = getVersion(address, key)
    eventContext.getLedger().dataAccount(address).setInt64(key, value, version)


# 获取数据最新版本
def getVersion(address, key):
    dataEntries = eventContext.getUncommittedLedger().getDataEntries(address, key)
    if None != dataEntries and dataEntries.length > 0:
        return dataEntries[0].getVersion()
    else:
        return -1


# 获取最新版本数据值
def getValue(address, key):
    version = getVersion(address, key)
    return getValueWithVersion(address, key, version)


# 获取指定版本数据值
def getValueWithVersion(address, key, version):
    dataEntry = eventContext.getUncommittedLedger().getDataEntry(address, key, version)
    if None != dataEntry:
        return dataEntry.getValue()
    else:
        return None


# 合约方法执行前操作
def beforeEvent(eventContext):
    return


# 合约方法执行后操作
def postEvent(eventContext, error):
    return
```

##  Wasm

JD Chain 从`1.6.4`版本实验性地加入`Wasm`以及`Rust`合约语言支持，`amd64-linux`

### Wasm运行时

基于[wasmer-java](https://github.com/wasmerio/wasmer-java)实现`Wasm`运行时

JD Chain 增加合约代码类型支持：`Rust`

### 合约设计

1. `jdcc_api`：

`Rust`合约服务，封装合约与`JD Chain`数据账户交互接口，供用户编写合约使用

```rs
// JD Chain Contract API

use std::ffi::{c_void, CStr, CString};
use std::mem;
use std::os::raw::c_char;

use crate::jdcc_types::*;

extern "C" {
    pub fn sys_call(req_len: i32, req_ptr: *mut c_char) -> usize;
    pub fn sys_msg(msg_len: i32, msg_ptr: *mut c_char) -> *mut c_char;
}

#[no_mangle]
pub extern fn allocate(size: usize) -> *mut c_void {
    let mut buffer = Vec::with_capacity(size);
    let ptr = buffer.as_mut_ptr();
    mem::forget(buffer);

    ptr as *mut c_void
}

#[no_mangle]
pub extern fn deallocate(ptr: *mut c_void, capacity: usize) {
    unsafe {
        let _ = Vec::from_raw_parts(ptr, 0, capacity);
    }
}

#[no_mangle]
pub extern fn drop_string(ptr: *mut c_char) {
    unsafe {
        let _ = CString::from_raw(ptr);
    }
}

// 账本服务接口
pub struct LedgerService {
    logger: Logger,
}

impl LedgerService {
    pub fn default() -> Self {
        LedgerService {
            logger: Logger {}
        }
    }

    fn call_and_get_sys_msg(&self, req: &str) -> &str {
        let req_len = req.len();
        let msg_len;
        unsafe {
            msg_len = sys_call(req_len as i32, CString::new(req).unwrap().into_raw()) as usize;
        };
        let msg_ptr = allocate(msg_len) as *mut c_char;
        let msg_ptr = unsafe {
            sys_msg(msg_len as i32, msg_ptr)
        };
        let msg = unsafe { CStr::from_ptr(msg_ptr).to_str().unwrap() };
        deallocate(msg_ptr as *mut c_void, msg_len);

        msg
    }

    pub fn logger(&self) -> &Logger {
        &self.logger
    }

    // 获取账本哈希
    pub fn get_ledger_hash(&self) -> Option<String> {
        let req = Request::get_ledger_hash();
        let ret = self.call_and_get_sys_msg(&req);
        let result: GetLedgerHashResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => GetLedgerHashResult { rc: ERROR, lh: None },
        };
        match result.rc {
            SUCCESS => result.lh,
            _ => None
        }
    }

    // 获取合约地址
    pub fn get_contract_address(&self) -> Option<String> {
        let req = Request::get_contract_address();
        let ret = self.call_and_get_sys_msg(&req);
        let result: GetContractAddressResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => GetContractAddressResult { rc: ERROR, ca: None },
        };
        match result.rc {
            SUCCESS => result.ca,
            _ => None
        }
    }

    // 获取交易哈希
    pub fn get_tx_hash(&self) -> Option<String> {
        let req = Request::get_tx_hash();
        let ret = self.call_and_get_sys_msg(&req);
        let result: GetTxHashResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => GetTxHashResult { rc: ERROR, th: None },
        };
        match result.rc {
            SUCCESS => result.th,
            _ => None
        }
    }

    // 获取交易时间
    pub fn get_tx_time(&self) -> Option<u64> {
        let req = Request::get_tx_time();
        let ret = self.call_and_get_sys_msg(&req);
        let result: GetTxTimeResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => GetTxTimeResult { rc: ERROR, tt: None },
        };
        match result.rc {
            SUCCESS => result.tt,
            _ => None
        }
    }

    // 获取交易签名用户地址列表
    pub fn get_signers(&self) -> Option<Vec<String>> {
        let req = Request::get_signers();
        let ret = self.call_and_get_sys_msg(&req);
        let result: GetSignersResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => GetSignersResult { rc: ERROR, ss: None },
        };
        match result.rc {
            SUCCESS => result.ss,
            _ => None
        }
    }

    // 注册用户
    pub fn register_user(&self, seed_ptr: *mut c_char) -> Option<String> {
        let seed = unsafe { CStr::from_ptr(seed_ptr).to_str().unwrap() };
        let req = Request::register_user(seed.to_string());
        let ret = self.call_and_get_sys_msg(&req);
        let result: RegisterUserResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => RegisterUserResult { rc: ERROR, a: None },
        };
        match result.rc {
            SUCCESS => result.a,
            _ => None
        }
    }

    // 查询用户
    pub fn get_user(&self, address_ptr: *mut c_char) -> Option<User> {
        let address = unsafe { CStr::from_ptr(address_ptr).to_str().unwrap() };
        let req = Request::get_user(address.to_string());
        let ret = self.call_and_get_sys_msg(&req);
        let result: GetUserResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => GetUserResult { rc: ERROR, a: None, pk: None },
        };
        match result.rc {
            SUCCESS => Some(User { address: result.a.unwrap(), pubkey: result.pk.unwrap() }),
            _ => None
        }
    }

    // 注册数据账户
    pub fn register_data_account(&self, seed_ptr: *mut c_char) -> Option<String> {
        let seed = unsafe { CStr::from_ptr(seed_ptr).to_str().unwrap() };
        let req = Request::register_data_account(seed.to_string());
        let ret = self.call_and_get_sys_msg(&req);
        let result: RegisterDataAccountResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => RegisterDataAccountResult { rc: ERROR, a: None },
        };
        match result.rc {
            SUCCESS => result.a,
            _ => None
        }
    }

    // 查询数据账户
    pub fn get_data_account(&self, address_ptr: *mut c_char) -> Option<DataAccount> {
        let address = unsafe { CStr::from_ptr(address_ptr).to_str().unwrap() };
        let req = Request::get_data_account(address.to_string());
        let ret = self.call_and_get_sys_msg(&req);
        let result: GetDataAccountResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => GetDataAccountResult { rc: ERROR, a: None, pk: None },
        };
        match result.rc {
            SUCCESS => Some(DataAccount { address: result.a.unwrap(), pubkey: result.pk.unwrap() }),
            _ => None
        }
    }

    // 写KV，字符类型，不带版本
    pub fn set_text(&self, addr_ptr: *mut c_char, key_ptr: *mut c_char, value_ptr: *mut c_char) -> Option<i64> {
        let address = unsafe { CStr::from_ptr(addr_ptr).to_str().unwrap() };
        let key = unsafe { CStr::from_ptr(key_ptr).to_str().unwrap() };
        let value = unsafe { CStr::from_ptr(value_ptr).to_str().unwrap() };
        let req = Request::set_text(address.to_string(), key.to_string(), value.to_string());
        let ret = self.call_and_get_sys_msg(&req);
        let result: SetKVResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => SetKVResult { rc: ERROR, ver: None },
        };
        match result.rc {
            SUCCESS => result.ver,
            _ => None
        }
    }

    // 写KV，字符类型
    pub fn set_text_with_version(&self, addr_ptr: *mut c_char, key_ptr: *mut c_char, value_ptr: *mut c_char, version: i64) -> Option<i64> {
        let address = unsafe { CStr::from_ptr(addr_ptr).to_str().unwrap() };
        let key = unsafe { CStr::from_ptr(key_ptr).to_str().unwrap() };
        let value = unsafe { CStr::from_ptr(value_ptr).to_str().unwrap() };
        let req = Request::set_text_with_version(address.to_string(), key.to_string(), value.to_string(), version);
        let ret = self.call_and_get_sys_msg(&req);
        let result: SetKVResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => SetKVResult { rc: ERROR, ver: None },
        };
        match result.rc {
            SUCCESS => result.ver,
            _ => None
        }
    }

    // 写KV，数值类型，不带版本
    pub fn set_int64(&self, addr_ptr: *mut c_char, key_ptr: *mut c_char, value: i64) -> Option<i64> {
        let address = unsafe { CStr::from_ptr(addr_ptr).to_str().unwrap() };
        let key = unsafe { CStr::from_ptr(key_ptr).to_str().unwrap() };
        let req = Request::set_int64(address.to_string(), key.to_string(), value);
        let ret = self.call_and_get_sys_msg(&req);
        let result: SetKVResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => SetKVResult { rc: ERROR, ver: None },
        };
        match result.rc {
            SUCCESS => result.ver,
            _ => None
        }
    }

    // 写KV，数值类型
    pub fn set_int64_with_version(&self, addr_ptr: *mut c_char, key_ptr: *mut c_char, value: i64, version: i64) -> Option<i64> {
        let address = unsafe { CStr::from_ptr(addr_ptr).to_str().unwrap() };
        let key = unsafe { CStr::from_ptr(key_ptr).to_str().unwrap() };
        let req = Request::set_int64_with_version(address.to_string(), key.to_string(), value, version);
        let ret = self.call_and_get_sys_msg(&req);
        let result: SetKVResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => SetKVResult { rc: ERROR, ver: None },
        };
        match result.rc {
            SUCCESS => result.ver,
            _ => None
        }
    }

    // 查询数据版本
    pub fn get_value_version(&self, addr_ptr: *mut c_char, key_ptr: *mut c_char) -> Option<i64> {
        let address = unsafe { CStr::from_ptr(addr_ptr).to_str().unwrap() };
        let key = unsafe { CStr::from_ptr(key_ptr).to_str().unwrap() };
        let req = Request::get_value_version(address.to_string(), key.to_string());
        let ret = self.call_and_get_sys_msg(&req);
        let result: GetValueVersionResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => GetValueVersionResult { rc: ERROR, ver: None },
        };
        match result.rc {
            SUCCESS => result.ver,
            _ => None
        }
    }

    // 查询数据
    pub fn get_value(&self, addr_ptr: *mut c_char, key_ptr: *mut c_char, version: i64) -> Option<KVData> {
        let address = unsafe { CStr::from_ptr(addr_ptr).to_str().unwrap() };
        let key = unsafe { CStr::from_ptr(key_ptr).to_str().unwrap() };
        let req = Request::get_value(address.to_string(), key.to_string(), version);
        let ret = self.call_and_get_sys_msg(&req);
        let result: GetValueResult = match serde_json::from_str(ret) {
            Ok(val) => val,
            Err(_) => GetValueResult { rc: ERROR, k: None, v: None, t: None, ver: None },
        };
        match result.rc {
            SUCCESS => Some(KVData {
                key: result.k.unwrap(),
                value: result.v.unwrap(),
                value_type: result.t.unwrap(),
                version: result.ver.unwrap(),
            }),
            _ => None
        }
    }
}

// 日志接口
pub struct Logger {}

impl Logger {
    pub fn debug(&self, msg: String) {
        let data = Request::log_debug(msg);
        let data_len = data.len() as i32;
        unsafe {
            sys_call(data_len, CString::new(data).unwrap().into_raw());
        }
    }

    pub fn info(&self, msg: String) {
        let data = Request::log_info(msg);
        let data_len = data.len() as i32;
        unsafe {
            sys_call(data_len, CString::new(data).unwrap().into_raw());
        }
    }

    pub fn error(&self, msg: String) {
        let data = Request::log_error(msg);
        let data_len = data.len() as i32;
        unsafe {
            sys_call(data_len, CString::new(data).unwrap().into_raw());
        }
    }
}
```
> 以上 API 为 JD Chain Rust合约接口标准实现，请勿随意修改
> Rust合约方法 参数仅支持`*mut c_char`和`i64`，方法返回值仅支持`*mut c_char`和数值类型

2. `sys_call`和`sys_msg`

`Wasm`调用账本数据库全部通过`sys_call`和`sys_msg`实现，`sys_call`完成操作并在运行时中缓存执行结构数据，返回执行结构数据长度；`sys_msg`则实现对执行结构数据的获取。

```rust
extern "C" {
    pub fn sys_call(req_len: i32, req_ptr: *mut c_char) -> usize;
    pub fn sys_msg(msg_len: i32, msg_ptr: *mut c_char) -> *mut c_char;
}
```

数据交互使用JSON序列化：

`request`：

```rs
rt req_type // 请求类型
d json // 具体操作请求体
```

`result`：
```rs
rc res_code // 返回状态码
d json // 具体操作返回体
```

> 参照 jdcc_types.rs 数据结构定义

3. `req_type`

请求类型：

- `0`：`log` 日志输出操作
- `1`：`before_event` 合约方法前置操作
- `2`：`post_event` 合约方法后置操作
- `3`：`get_ledger_hash`  获取账本哈希
- `4`：`get_contract_address`  获取当前合约地址
- `5`：`get_tx_hash` 获取当前交易哈希
- `6`：`get_tx_time` 获取当前交易时间
- `7`：`get_signers` 获取当前交易签名列表
- `8`：`register_user` 注册用户
- `9`：`get_user` 查询用户
- `10`：`register_data_account` 注册数据账户
- `11`：`get_data_account` 获取数据账户
- `12`：`set_text` 写KV，字符类型，自动版本
- `13`：`set_text_with_version` 写KV，字符类型，用户提供版本
- `14`：`set_int64` 写KV，long类型，自动版本
- `15`：`set_int64_with_version` 写KV，long类型，用户提供版本
- `16`：`get_value_version` 查询数据版本
- `17`：`get_value` 查询KV数据

4. `res_code`

- `0`：`success`
- `1`：`error`

### 部署使用

1. 安装 Rust
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

2. 安装 wasm-pack
```bash
curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh
```

3. 创建合约项目

```bash
git clone git@github.com:blockchain-jd-com/jdchain-rust-contract.git
cd jdchain-rust-contract
git checkout master
```

4. 修改`sample_contract.rs`

根据自身业务需求修改`sample_contract.rs`合约代码，暴露对外提供的合约方法：

5. 编译
```bash
wasm-pack build . --release
```

6. 合约部署
```bash
./jdchain-cli.sh tx contract-deploy --code jdchain-rust-contract/pkg/jdchain_rust_contract_bg.wasm --lang Wasm --pubkey 7VeRG8jpBNg15W7HCrFyLG7TdpUea5jnHAUDbmxAkK6ZYqu4
```

7. 合约调用
```bash
./jdchain-cli.sh tx contract --address LdeNgGn7tPYXNi4vAhXN57qAYtb57NvAUDvvg --method get_ledger_hash
```

## 合约安全

`Java`语言合约安全基于编译插件黑名单、运行时`Security Manager`来保证。
`GraalVM`合约语言基于[互操作性安全控制](https://www.graalvm.org/22.0/security-guide/)保证。

## SDK

除上述使用 maven 命令方式部署合约外，JD Chain SDK 提供了 Java 和 Go 语言的合约部署/升级，合约调用等方法。
以下以 Java SDK 为例讲述主要步骤，完整代码参照[JD Chain Samples](https://github.com/blockchain-jd-com/jdchain-samples)合约部分。

### 合约部署

```java
// 新建交易
TransactionTemplate txTemp = blockchainService.newTransaction(ledger);
// 生成合约账户
BlockchainKeypair contractAccount = BlockchainKeyGenerator.getInstance().generate();
System.out.println("合约地址：" + contractAccount.getAddress());
// 部署合约
txTemp.contracts().deploy(contractAccount.getIdentity(), FileUtils.readBytes("src/main/resources/contract-samples-1.4.2.RELEASE.car"));
```

### 合约升级

```java
// 新建交易
TransactionTemplate txTemp = blockchainService.newTransaction(ledger);
// 解析合约身份信息
BlockchainIdentity contractIdentity = new BlockchainIdentityData(KeyGenUtils.decodePubKey("7VeRCfSaoBW3uRuvTqVb26PYTNwvQ1iZ5HBY92YKpEVN7Qht"));
System.out.println("合约地址：" + contractIdentity.getAddress());
// 指定合约地址，升级合约，如合约地址不存在会创建该合约账户
txTemp.contracts().deploy(contractIdentity, FileUtils.readBytes("src/main/resources/contract-samples-1.4.2.RELEASE.car"));
```

### 合约调用

1. 动态代理方式

基于动态代理方式合约调用，需要依赖合约接口

```java
// 新建交易
TransactionTemplate txTemp = blockchainService.newTransaction(ledger);

// 一次交易中可调用多个（多次调用）合约方法
// 调用合约的 registerUser 方法
SampleContract sampleContract = txTemp.contract("LdeNr7H1CUbqe3kWjwPwiqHcmd86zEQz2VRye", SampleContract.class);
GenericValueHolder<String> userAddress = ContractReturnValue.decode(sampleContract.registerUser(UUID.randomUUID().toString()));

// 准备交易
PreparedTransaction ptx = txTemp.prepare();
// 交易签名
ptx.sign(adminKey);
// 提交交易
TransactionResponse response = ptx.commit();
Assert.assertTrue(response.isSuccess());

// 获取返回值
System.out.println(userAddress.get());
```

2. 非动态代理方式

不需要依赖合约接口及实现，传入参数构造合约调用操作

```java
// 新建交易
TransactionTemplate txTemp = blockchainService.newTransaction(ledger);

ContractEventSendOperationBuilder builder = txTemp.contract("LdeNrPJtkAi55dzM5C73q9cXMxffVLiEEW7MW");
// 运行前，填写正确的合约地址，数据账户地址等参数
// 一次交易中可调用多个（多次调用）合约方法
// 调用合约的 registerUser 方法，传入合约地址，合约方法名，合约方法参数列表
builder.invoke("registerUser",
        new BytesDataList(new TypedValue[]{
                TypedValue.fromText(UUID.randomUUID().toString())
        })
);
// 准备交易
PreparedTransaction ptx = txTemp.prepare();
// 交易签名
ptx.sign(adminKey);
// 提交交易
TransactionResponse response = ptx.commit();
Assert.assertTrue(response.isSuccess());

Assert.assertEquals(1, response.getOperationResults().length);
// 解析合约方法调用返回值
for (int i = 0; i < response.getOperationResults().length; i++) {
    BytesValue content = response.getOperationResults()[i].getResult();
    switch (content.getType()) {
        case TEXT:
            System.out.println(content.getBytes().toUTF8String());
            break;
        case INT64:
            System.out.println(BytesUtils.toLong(content.getBytes().toBytes()));
            break;
        case BOOLEAN:
            System.out.println(BytesUtils.toBoolean(content.getBytes().toBytes()[0]));
            break;
        default: // byte[], Bytes
            System.out.println(content.getBytes().toBase58());
            break;
    }
}
```

### 更新合约状态

```java
// 新建交易
TransactionTemplate txTemp = blockchainService.newTransaction(ledger);
// 合约状态分为：NORMAL（正常） FREEZE（冻结） REVOKE（销毁）
// 冻结合约
txTemp.contract("LdeNr7H1CUbqe3kWjwPwiqHcmd86zEQz2VRye").state(AccountState.FREEZE);
// 交易准备
PreparedTransaction ptx = txTemp.prepare();
// 交易签名
ptx.sign(adminKey);
// 提交交易
TransactionResponse response = ptx.commit();
Assert.assertTrue(response.isSuccess());
```

### 更新合约权限

```java
// 新建交易
TransactionTemplate txTemp = blockchainService.newTransaction(ledger);
// 配置合约权限
// 如下配置表示仅有 ROLE 角色用户才有调用 LdeNr7H1CUbqe3kWjwPwiqHcmd86zEQz2VRye 权限
txTemp.contract("LdeNr7H1CUbqe3kWjwPwiqHcmd86zEQz2VRye").permission().mode(70).role("ROLE");
// 交易准备
PreparedTransaction ptx = txTemp.prepare();
// 交易签名
ptx.sign(adminKey);
// 提交交易
TransactionResponse response = ptx.commit();
Assert.assertTrue(response.isSuccess());
```

Python Library For BCX
==============
* [入门](#入门)
* [使用API](#使用API)

入门
---------------

我们建议在Ubuntu 16.04 LTS（64位）上构建 ，默认python3.5

**手动安装：**

    cd python-grapheneEnhance
    python3 setup.py install --user
	
**修改链参数：**

    vi python-grapheneEnhance/grapheneEnhancebase/chains.py # 编辑链相关参数
	
	```python
	known_chains = {
    "xxxxxx": {
        "chain_id": "xxxxxx",
        "core_symbol": "xxxxxx",
        "prefix": "xxxxxx"} # chains.py中所编辑的代码
	```
	python3 setup.py install --user # 重新加载python库

**构建pyhton脚本：**
```python
from grapheneEnhance.graphene import Graphene
from grapheneEnhance.instance import set_shared_graphene_instance
from grapheneEnhance.storage import configStorage as config
from pprint import pprint

nodeAddress = "ws://127.0.0.1:8000" # 所需要连接的RPC节点
gph = Graphene(node=nodeAddress, blocking=True) # 实例化对象
set_shared_graphene_instance(gph) # 将gph设置为共享的全局实例

if gph.wallet.created() is False: # 创建本地钱包数据库，如果没有，则创建一个新的钱包数据库
    gph.newWallet("xxxxxx")
gph.wallet.unlock("xxxxxx") # 解锁钱包，若后续操作需要与钱包交互，则需要解锁钱包

config["default_prefix"] = gph.rpc.chain_params["prefix"] # 向钱包数据库中添加默认信息
gph.wallet.addPrivateKey(privateKey) # 向钱包中添加私钥
config["default_account"] = yourname # 向钱包数据库中添加默认信息
```

使用API
-------------
**Graphene实例调用示例：**  
方法：info  
功能：获得链的全局属性  
示例：  
```python
print(gph.info())
```
方法：transfer  
功能：向目标对象发送代币  
参数：  
    to：接收方账户名  
    amount(int)：发送的代币数量  
    asset：资产ID或代币符号  
    memo：转账备注  
    account：发送方账户名  
示例：
```python
pprint(gph.transfer("test2",100, "1.3.0", " ", "test1"))
```
方法：upgrade_account  
功能：将账户升级为终身账户，可以创建子账户，此操作需要消耗一定的手续费  
参数：  
    account：升级的账户  
示例：
```python
pprint(gph.upgrade_account("test1"))
```
方法：create_account  
功能：创建一个账户并将私钥导入到钱包  
参数：  
    account_name：账户名注册规则，/^[a-z][a-z0-9\.-]{4,63}$/，小写字母开头+数字或小写字母或点.或短横线-，长度4至63  
    password：账户密码  
注：只有终身账户才可以创建账户  
示例：  
```python
pprint(gph.create_account("test3", "password"))
```
**钱包api调用示例：**  
方法：unlock  
功能：解锁钱包，进行相关钱包操作  
参数：  
	pwd：钱包密码  
示例：  
```python
print(gph.wallet.unlock(pwd))
```
方法：getAccounts  
功能：获取钱包数据库中的账户信息  
```python
print(gph.wallet.getAccounts())
```
方法：getPrivateKeyForPublicKey  
功能：根据所给出的公钥在钱包中获取对应的私钥  
参数：  
	pub：公钥字符串  
```python
print(gph.wallet.getPrivateKeyForPublicKey(pub))
```
**RPC接口调用示例：**  
方法：get_object   
功能：获取此object对象信息  
参数：  
	object_id：对象id  
示例：
```python
print(gph.rpc.get_object("1.2.18")) # 获取id为"1.2.17"的账户信息
```
方法：get_contract    
功能：获取合约的详细信息  
参数：  
	contract_id：合约id或合约名称
示例：  
```python
print(gph.rpc.get_contract("1.16.0")) # 获取id为"1.16.0"的合约信息
```



 

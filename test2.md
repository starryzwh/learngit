# Part 1. Cocos-BCX链系统互操作API

Python API，用于使用COCOS-BCX RPC API与基于COCOS-BCX的区块链集成。

## 类库引用说明

### 引入API文件

```python
from grapheneEnhance.graphene import Graphene
```
 
### 实例化类库对象

```Python
gph = Graphene(
    node="ws://XXXXXXXXX" #节点rpc地址
    )
```

### 调用实例-转账

```Python
gph.transfer(
    to="test",
    amount=1,
    asset="COCOS",
    memo="",
    account="developer"
    )
```  

## API说明

#### 1.接口的参数类型没有特殊说明均为字符串
#### 2.接口的参数没有特殊说明均不能为空
#### 3.查询类接口返回数据实例:{code:1,data:[]}


# Part 2. 区块链系统的互操作API  

### 转账
方法：transfer  
功能：向目标对象发送代币  
参数：  
    to：接收方账户名  
    amount(int)：发送的代币数量  
    asset：资产ID或代币符号  
    memo：转账备注  
    account：发送方账户名  

### 创建限价单
方法：limit_order_create  
功能：创建在给定市场中的订单  
参数：
    amount(int)：出售的代币数量  
    asset：出售的资产ID或代币符号  
    min_amount(int)：所要求获取的代币的最低数量  
    min_amount_asset：所要求获取的资产ID或代币符号  
    fill(bool)：默认为False，如果这个标志被设置为True，那么这个订单必须被完整的购买或者被拒绝  
    account：出售者账户名  
    
### 取消限价单
方法：limit_order_cancel  
功能：取消您在给定市场中的订单  
参数：  
    order_number(list)：要取消的限价单的ID  
    account：操作人账户名  
    
### 调整抵押单
方法：call_order_update  
功能：调整抵押单内容  
参数：  
    amount：增加到保证金的金额数量  
    asset：增加到保证金的资产ID  
    _amount：要偿还的债务金额数量  
    _asset：要偿还的债务资产ID  
    account：资金提供者账户名  

### 创建账户
方法：create_account  
功能：创建一个账户并将私钥导入到钱包  
参数：  
    account_name：账户名注册规则，/^[a-z][a-z0-9\.-]{4,63}$/，小写字母开头+数字或小写字母或点.或短横线-，长度4至63  
    password：账户密码  
    
### 添加账户黑白名单
方法：account_whitelist  
功能：为该账户指定名单类型添加账户  
参数：  
    account_to_list：被添加的账户  
    account_listing：名单类型，默认为no_listing  
    account：操作者账户名  
名单类型：  
    no_listing = 0x0  
    white_listing = 0x1  
    black_listing = 0x2  
    white_and_black_listed = white_listed | black_listed  

### 升级账户
方法：upgrade_account  
功能：将账户升级为终身账户，可以创建子账户，此操作需要消耗一定的手续费  
参数：  
    account：升级的账户  

### 创建资产
方法：asset_create  
功能：创建token  
参数：  
    symbol：资产符号，正则^\[\.A-Z\]+$  
    precision(int)：精度(小数位数)  
    amount(int)：基准资产数量(即创建的代币，默认1)  
    asset：基准资产ID  
    _amount(int)：标价资产(即核心资产，默认1)  
    _asset：标价资产  
    common_options(dict)：代币选项  
    bitasset_opts(dict)：比特代币选项(非必填)，若使用默认参数来创建比特代币，则只需传入{}即可  
    is_prediction_market(bool)：是否为预测市场(非比特代币无需关注此参数)  
    account：代币创建者  
commen_options参数示例：  
```Python
common_options = {
    "max_supply": 10000000000000, # 最大发行量
    "market_fee_percent": 0, # 市场交易手续费百分比，默认
    "max_market_fee": 0, # 市场交易手续费最大值，默认
    "issuer_permissions": 79, # 发行者可以更新的权限，默认
    "flags": 0, # 当前权限
    "core_exchange_rate": {"base": {}, "quote": {}}, # 与核心资产的转换率，由上述基准资产与标价资产决定
    "whitelist_authorities": [], # 白名单账户
    "blacklist_authorities": [], # 黑名单账户
    "whitelist_markets": [], # 白名单资产
    "blacklist_markets": [], # 黑名单资产
    "description": '{"main":"","short_name":"","market":""}', #内容描述
    "extension": {}
}
```

### 更新资产
方法：asset_update  
功能：更新token  
参数：  
    asset：需要更新的资产符号或者资产ID  
    new_options(dict)：新的代币选项  
    issuer：新的代币创建者  
    account：代币创建者  
new_options参数示例：  
```Python
new_options = {
    "max_supply": 10000000000000,
    "market_fee_percent": 0,
    "max_market_fee": 0,
    "issuer_permissions": 79,
    "flags": 0,
    "core_exchange_rate": {"base": {}, "quote": {}},
    "whitelist_authorities": [],
    "blacklist_authorities": [],
    "whitelist_markets": [],
    "blacklist_markets": [],
    "description": '{"main":"","short_name":"","market":""}',
    "extension": {}
}
```

### 更新智能资产
方法：asset_update_bitasset  
功能：更新bitAsset  
参数：  
    asset：更新的智能资产ID或者智能资产符号  
    short_backing_asset：做空时抵押的资产ID或者资产符号  
    new_options：新的比特代币选项  
    account：发行者账户名  
new_options参数示例：  
```Python
new_options = {
    "feed_lifetime_sec": 60*60, # 喂价有效时间
    "minimum_feeds": 1, # 最少喂价数量
    "force_settlement_delay_sec": 60*60, # 强制清算发生前延迟时间
    "force_settlement_offset_percent": 1, # 强制清算价格偏离百分比
    "maximum_force_settlement_volume": 2000, # 强制清算最大数量
    "short_backing_asset": "1.3.0", # 做空时抵押的资产类型
    "extensions": {}
}
```
    
### 修改智能资产喂价账户
方法：asset_update_feed_producers  
功能：修改智能资产的喂价账户  
参数：  
    asset：要修改的智能资产ID或者智能资产符号  
    feed_producers(list)：新的喂价账户  
    account：资产发行者  
    
### 资产发行
方法：asset_issue  
功能：代币资产token发行  
参数：  
    amount(int)：发行数量  
    asset：发行资产符号  
    issue_to_account：发行对象  
    memo：附加消息(非必填)  
    account：代币创建者  
    
### 资产销毁
方法：asset_reserve  
功能：放弃部分资产，指定资产将被回收，流通量减少，总量不变  
参数：  
    amount：销毁的资产数量  
    asset：销毁的资产符号  
    account：销毁者  
    
### 注资资产手续费池
方法：asset_fund_fee_pool  
功能：所有网络手续费最终将使用核心资产代币进行支付。手续费资金池用来承担从二级资产代币转换为核心资产代币的费用，以便用户可以使用二级资产代币来支付手续费。如果资金池中余额用完，用户将无法继续使用二级资产代币支付手续费。目前支持使用二级资产代币作为手续费的API有“转账、投票、升级终身会员、资产发行”，后续会继续扩展  
参数：  
    asset：需要注资的二级资产代币符号  
    amount：注资核心资产代币数量  
    account：注资者  
    
### 清算bitasset资产
方法：asset_settle  
功能：清算bitasset资产  
参数：  
    amount：清算数量  
    asset：清算资产ID或资产符号  
    account：要求清算的账户  
    
### 清算货币交易市场
方法：asset_global_settle  
功能：在global_settle允许的情况下清算货币交易市场  
参数：  
    asset_to_settle：清算的资产ID或资产符号  
    settle_price(dict)：清算价格  
    account：资产发行者  
settle_price参数示例：      
```Pyhton
settle_price = {
    "base": {"amount": 1, "asset_id": "1.3.1"}, #基准资产
    "quote": {"amount": 1, "asset_id": "1.3.0"} #标价资产
}
```
    
### 发行资产喂价
方法：publish_price_feed  
功能：发行资产喂价  
参数：  
    symbol：喂价的资产符号  
    settlement_price(dict)：清算价格  
    cer(dict)：core_exchange_rate 核心资产转化率  
    account：发行者  
settlement_price参数示例：  
```Python
settlement_price = {
    "base": {
        "amount": 基准资产数量,
        "asset_id": 基准资产ID 
    },
    "quote": {
        "amount": 标价资产数量,
        "asset_id": 标价资产ID
    }
}
```
cer参数示例：  
```Python
cer = {
    "base": {
        "amount": 基准资产数量,
        "asset_id": 基准资产ID 
    },
    "quote": {
        "amount": 标价资产数量,
        "asset_id": 标价资产ID
    }
}
```

### 创建见证人候选人
方法：create_witness  
功能：创建见证人候选人  
参数：  
    account_name：见证人候选人账户  
    url：见证人网页链接  
    key：见证人块签名公钥  
    
### 见证人投票
方法：approve_witness  
功能：为见证人候选人投票  
参数：  
    witnesses(list)：见证人账户名或见证人ID  
    account：投票账户名  
    
### 拒绝见证人投票
方法：disapprove_witness  
功能：拒绝为见证人候选人投票  
参数：  
    witnesses(list)：见证人账户名或见证人ID  
    account：不赞成者账户名  
    
### 更新见证人
方法：update_witness  
功能：更新见证人  
参数：  
    witness_identifier：所更新的见证人  
    url：新的见证人网页链接  
    key：新的见证人块签名公钥  
    
### 批准提议
方法：approveproposal  
功能：批准一个提议  
参数：  
    proposal_ids(list)：提议ID  
    account：更新提议的账户  
    
### 撤销提议
方法：disapproveproposal  
功能：不赞成一个提议  
参数：  
    proposal_ids(list)：提议ID  
    account：更新提议的账户  
    
### 删除提议
方法：proposal_delete  
功能：删除一个提议  
参数：  
    proposal：要删除的提议ID  
    account：删除提议的账户  
    
### 设置保留金
方法：withdraw_permission_create  
功能：设置保留金，允许被授权人分期解冻领取保留金  
参数：  
    authorized_account：可以领取该保留金的账户  
    amount(int)：保留金数量  
    asset：保留金资产符号或ID  
    period_start_time：第一个提款期开始的时间(时间格式为"Year-Month-DayTHours:Minutes:seconds")，示例"1970-01-01T00:00:00"  
    withdrawal_period_sec：保留金提取期长度(单位为S)，默认为0  
    periods_until_expiration：此权限有效的提款期数，默认为0  
    account：设置保留金的账户  
    
### 更新保留金
方法：withdraw_permission_update  
功能：更新保留金解冻规则  
参数：  
    authorized_account：可以领取该保留金的账户  
    permission_to_update：需要更新的保留金ID  
    amount(int)：保留金数量  
    asset：保留金资产符号或ID  
    period_start_time：第一个提款期开始的时间(时间格式为"Year-Month-DayTHours:Minutes:seconds")，示例"1970-01-01T00:00:00"  
    withdrawal_period_sec：保留金提取期长度(单位为S)，默认为0  
    periods_until_expiration：此权限有效的提款期数，默认为0  
    account：更新保留金的账户  
    
### 领取保留金
方法：withdraw_permission_claim  
功能：由被授权人的账户领取保留金  
参数：  
    withdraw_permission：领取的保留金ID  
    withdraw_from_account：设置保留金的账户  
    amount(int)：保留金数量  
    asset：保留金资产符号或ID  
    memo：备注留言，默认为空，可不填  
    account：领取保留金的账户  
    
### 删除保留金
方法：withdraw_permission_delete  
功能：删除保留金资助合约  
参数：  
    authorized_account：领取该保留金的账户  
    withdrawal_permission：需要删除的保留金ID  
    account：删除保留金的账户  
    
### 创建理事会候选人
方法：committee_member_create  
功能：创建理事会候选人  
参数：  
    url：网页链接  
    account：理事会候选人的账户  
    
### 理事会投票
方法：approve_committee  
功能：为理事会候选人投票  
参数：  
    committees(list)：理事会候选人账户名或理事会候选人ID  
    account：投票者账户名  

### 拒绝理事会投票
方法：disapprove_committee  
功能：拒绝为理事会候选人投票  
参数：  
    committees(list)：理事会候选人账户名或理事会候选人ID  
    account：拒绝者账户名  
    
### 更新理事会候选人
方法：committee_member_update  
功能：跟新理事会候选人  
参数：  
    new_url：新的网页链接  
    account：更新的理事会候选人的账户  
    
### 保留资金
方法：vesting_balance_create  
功能：保留资金，锁定资金  
参数：  
    owner：领取者账户  
    amount(int)：资产数量  
    asset：资产符号  
    start：开始领取时间(时间格式为"Year-Month-DayTHours:Minutes:seconds")，示例"1970-01-01T00:00:00"  
    _type：领取规则类型，默认为"cdd"，还有线性"linear"  
    account：创建者账户  
    
### 领取解冻的保留资金
方法：vesting_balance_withdraw  
功能：领取解冻的保留资金  
参数：  
    vesting_id：领取的保留资金ID  
    amount(int)：领取的保留资金的数量   
    asset：领取的保留资金符号或ID  
    account：领取人的账户名  
    
### 创建工人
方法：create_worker  
功能：创建一个工人  
参数：  
    name：项目名称  
    daily_pay：每日开销  
    end：项目结束时间(时间格式为"Year-Month-DayTHours:Minutes:seconds")，示例"1970-01-01T00:00:00"  
    url：网页链接  
    begin：项目开始时间(时间格式为"Year-Month-DayTHours:Minutes:seconds")，示例"1970-01-01T00:00:00"  
    payment_type：支付类型，默认为"vesting"，还有"refund"、"burn"  
    pay_vesting_period_days：每天支付金额，默认为0  
    account：创建人账户名  
    
### 工人投票
方法：approve_worker  
功能：为创建的工人投票  
参数：  
    workers(list)：工人账户名或ID  
    account：投票人账户名  
    
### 拒绝工人投票
方法：disapprove_worker  
功能：不赞成为此工人投票  
参数：  
    workers(list)：工人账户名或ID  
    account：拒绝人账户名  
    
### 智能资产抵押投标
方法：bid_collateral  
功能：智能资产抵押投标  
参数：  
    amount：投标金额数量  
    asset：投标金额资产ID或资产符号  
    debt_amount：所抵押的金额数量  
    debt_asset：所抵押的金额资产ID或资产符号  
    account：投标者账户名  

### 创建合约
方法：create_contract  
功能：创建智能合约  
参数：  
    name：合约名，正则/^[a-z][a-z0-9\.-]{4,63}$/，首字母开头+字母或数字或点.或短横线-，长度4至63  
    data：合约lua代码  
    con_authority：合约权限(一对公私钥中的公钥publicKey)  
    account：合约创建者  
    
### 调用合约函数
方法：call_contract_function  
功能：调用合约函数接口  
参数：  
    contract：合约名称或者合约ID  
    function：合约中函数名称  
    value_list(list)：调用合约函数的参数列表  
    account：调用者账户名  
value_list参数示例：  
```Python
value_list = [
        [2, {"baseValue": "4.2.5"}], 
        [2, {"baseValue": json.dumps({"size": "small"})}]
    ]
```
    
### 更新合约
方法：revise_contract   
功能：更新合约代码  
参数：  
    contract：合约名称或者合约ID  
    data：合约lua代码  
    account：合约创建者  
    
### 注册开发者
方法：register_nh_asset_creator  
功能：将当前账户注册成为开发者  
参数：  
    account：注册者账户名  
    
### 创建世界观
方法：create_world_view  
功能：创建支持的NH资产世界观，向区块链系统注册当前账号（通常为游戏的账号）支持的NH资产世界观  
参数：  
    world_view：世界观名称  
    account：创建者账户名  
    
### 提议关联世界观
方法：relate_world_view  
功能：关联世界观，开发者只有在关联了某一个世界观后，才可以创建这个世界观的NH资产，该操作需要通过提议来完成，需要此世界观的创建者审批  
参数：  
    world_view：世界观名称  
    account：关联人账户名  
    
### 批准关联世界观的提议
方法：approveproposal  
功能：批准其他用户关联自己的世界观的提议  
参数：  
    proposal_ids(list)：提议ID  
    account：更新提议的账户  
    
### 创建NH资产
方法：create_nh_asset  
功能：创建一个唯一的NH资产，具有唯一性  
参数：  
    owner：指定NH资产拥有者(NH资产归属权账户，默认为NH资产创建者)  
    assetID：当前NH资产交易时，使用的资产符号  
    world_view：世界观  
    describe：当前NH资产的具体内容描述，由制造者定义  
    account：创建者  
    
### 关联NH资产
方法：relate_nh_asset  
功能：为当前NH资产关联父子NH资产  
参数：  
    parent：父NH资产ID  
    child：子NH资产ID  
    contract：合约ID  
    relate：是否关联，默认为True，False为取消关联  
    account：NH资产创建者  
    
### 删除NH资产
方法：delete_nh_asset  
功能：删除整条NH资产数据记录，通常在商品销毁时使用(仅能由用户自己授权处理自己想要销毁的数据)  
参数：  
    asset_id：NH资产ID  
    account：删除者，必须为给NH资产拥有者  
    
### 转移NH资产
方法：transfer_nh_asset  
功能：用户可以将自己的NH资产转移到另外一个用户  
参数：  
    to：转移NH资产的目标用户名  
    nh_asset_id：NH资产ID  
    account：转移者账户名  
    
### 创建NH资产出售单
方法：create_nh_asset_order  
功能：卖出NH资产  
参数：  
    otcaccount：OTC交易平台账户，用于收取挂单费用  
    pending_order_fee_amount：挂单费用数量，用户向OTC平台账户支付的挂单费用  
    pending_order_fee_asset：挂单费用所用资产符号或ID，用户向OTC平台账户支付的挂单费用  
    nh_asset：NH资产ID  
    memo：挂单备注信息  
    price_amount：商品挂单价格数量  
    price：商品挂单价格所用资产符号或ID  
    account：挂单人  
    
### 取消NH资产出售单
方法：cancel_nh_asset_order  
功能：取消NH资产挂卖订单  
参数：  
    order：订单ID  
    account：取消者账户名  
    
### 购买NH资产出售单
方法：fill_nh_asset_order  
功能：买入NH资产，支付购买游戏装备的代币费用，同时修改用户拥有的商品数据。该操作是一个多步合成的原子操作，在支付费用的同时完成用户账户NH资产数据的更新，如果支付动作或账户商品数据更新动作中某一个动作不被主链区块认可，则整个交易将被回滚，避免异常交易。  
参数：  
    order：订单ID  
    account：购买者账户名  
    
### 创建文件
方法：create_file  
功能：创建一个文件  
参数：  
    filename：文件名称  
    content：文件内容  
    account：创建者  
    
### 添加文件关联人
方法：add_file_relate_account  
功能：可为文件添加多个关联人  
参数：  
    file：文件名或者文件ID  
    related_account(list)：所关联的账户名，参数示例：["XXX", "XXX", ...]  
    account：文件创建者  
    
### 签名文件
方法：file_signature  
功能：为文件签名，签名须为文件关联人签名  
参数：  
    file：文件名或者文件ID  
    signature：签名内容  
    account：签名者  
    
### 关联父子文件
方法：relate_parent_file  
功能：为文件关联父子文件，此操作需要提议来完成，需要父文件拥有者批准提议  
参数：  
    parent_file：父文件名或者父文件ID  
    sub_file：子文件名或者子文件ID  
    
### 批准关联父子文件的提议
方法：approveproposal  
功能：父文件创建者批准关联到此文件的子文件  
参数：  
    proposal_ids(list)：提议ID  
    account：更新提议的账户名  
    
### 创建定时任务
方法：crontab_create  
功能：创建一个定时任务  
参数：  
    crontab_ops(list)：定时任务的内容  
    start_time：定时任务的开始时间(时间格式为"Year-Month-DayTHours:Minutes:seconds")，示例"1970-01-01T00:00:00"  
    interval(int)：任务执行间隔  
    times(int)：任务计划执行次数  
    account：创建者账户名  
参数crontab_ops示例：  
```Python
crontab_ops = [
        {
            "op":[0, 
                {
                    'from': '1.2.18', 
                    'fee': {'asset_id': '1.3.0', 'amount': 2000000}, 
                    'to': '1.2.25', 
                    'amount': {'asset_id': '1.3.0', 'amount': 1000000}, 
                    'extensions': []
                }
            ]
        }
    ]
```

### 取消定时任务
方法：crontab_cancel  
功能：取消一个定时任务  
参数：  
    task：定时任务ID  
    account：取消者账户名  
    

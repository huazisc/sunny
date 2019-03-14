## 商城兑换服务
[TOC]

### 1. 包名：glgo/base/mall
### 2. 服务概要
提供商城兑换接口；包括兑换道具、钻石兑金币
### 3. 设计说明
1. 先验证商品信息、价格、支付方式等参数是否匹配；
2. 检查钱是否足够，足够的情况下先进行扣费；
3. 扣费完成后发货；
4. 若发货失败，记录失败日志，并退费；
5. 折扣、加赠等逻辑后续会添加；

```
graph TD
    A[验证参数有效性]-->B[验证商品是否存在]
    B-->C{钱够?}
    C-->|否|ZZ[结束]
    C-->|是|D[扣费]
    D-->E[发货]
    E-->F{发货成功}
    F-->|是|G[写兑换日志]
    G-->ZZ
    F-->|否|I[写失败日志]
    I-->H[退还费用]
    H-->ZZ
```

### 4. 接口说明
- 服务名：MallService.ExchangeGoods
- 请求包：
```go
type ExchangeReq struct {
	Uid                  int64    
	ItemType             int32    //物品类型
	Gid                  int32    //商品ID
	Price                int32    
	GNum                 int32    //商品数量
	Pmode                int32    //支付方式 2：钻石；3：金币
}
```
- 返回包：
```go
//货币变化情况
type MoneyInfo struct {
	CurrencyType         int32    //1：钻石 2：金币
	Before               int64    
	Changed              int64    
}

type ExchangeRes struct {
	Code                 int32       
	Msg                  string      
	MoneyInfo            []MoneyInfo 
	ExtInfo              string      //扩展信息，暂时没有用
}
```

### 5. 返回错误码：
```
	0 : 成功
	1 //参数错误
	2 //支付方式错误
	3 //查无此商品
	4 //价格不匹配
	5  //扣费失败
	6  //发货（加钱）失败
	7 //钱不够
```	
	
### 更新记录
版本号 | 操作人 | 更新时间|修改内容 
:--: | :-- |:-- | :--
v1.0 | 戴华 | 2019-3-13 | 创建文档


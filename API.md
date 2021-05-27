# 【平台调用商城】接口列表(商城提供)

---

1. 用户登录获取token接口
2. 跳转商城登录页面,进入商城
3. 商城初始化生成兑换流水号
4. 通知商城用户兑换积分接口
5. 积分兑换查询接口
6. 积分扣减初始化流水号接口
7. 通知商城用户积分扣减接口
8. 订单结算接口
9. 查询积分接口

---

**签名规则**

``` javascript
传入参数按参数名的字母顺序升序排列（不包含signature自身），
将参数值拼接后加上 APP_SECRET（配置文件设定）,做 MD5 Hash。 

PS : /xx?name=scott&age=11&sex=2 

参数名经过排序后:age=11&name=scott&sex=2 对应参数值拼接后：11scott2, 
假设 APP_SECRET 是：app123, 拼接后： 11scott2app123，
md5 hash 后的结果(32 位小写)： 59d76bac25862ab8edc48f427862ff39

即： signature=59d76bac25862ab8edc48f427862ff39


PHP DEMO

    /**
     * 获取签名
     * @param $data
     * @return mixed
     */
    public function getSignature($data){
        $app_secret = env('APP_SECRET');
        ksort($data);
        $str = implode('', $data) . $app_secret;
        return md5($str);
    }
```

## 用户登录获取token接口

接口功能

> 该接口用于获取用户登录信息，用于跳转到商城登录页面使用。

URL

> /api/v2/user/fast-login

HTTP请求方式

> POST

请求参数

| 参数         | 必选 | 类型   | 说明             |
| :----------- | :--- | :----- | ---------------- |
| appid        | true | string | 应用id           |
| p_user_id    | true | string | 平台的用户标识   |
| mobile_phone | true | string | 平台的用户手机号 |
| timestamp    | true | string | 时间戳           |
| signature    | true | string | 签名             |

返回字段

<table>
  	<tr>
		<td>返回字段</td>
		<td>字段类型</td>
        <td colspan="2">说明</td>
	<tr>
	<tr>
		<td>code</td>
		<td>int</td>
        <td colspan="2">返回值编码(0:执行成功) </td>
	</tr>
    <tr>
		<td>msg</td>
		<td>string</td>
        <td colspan="2">返回值消息</td>
	</tr>
	<tr>
        <td rowspan="2">data</td>
        <td rowspan="2">json</td>
        <td>p_user_id</td>
        <td>平台用户标识</td>
	</tr>
    <tr>
        <td>token</td>
        <td>登录token</td>
	</tr>
</table>

返回值示例

``` javascript
{
    "code": 0,
    "msg": "success",
    "data": {
        "p_user_id":"10001"
        "token":"xxxxxxxxxxxxxxxxx"
    }
}
```

## 跳转商城登录页面进入商城

接口功能

> 该接口用于登录商城系统，使用location方式跳转至商城。

URL

> /login

HTTP请求方式

> GET

请求参数

| 参数      | 必选 | 类型   | 说明         |
| :-------- | :--- | :----- | ------------ |
| appid     | true | string | 应用id       |
| p_user_id | true | string | 平台用户标识 |
| token     | true | string | token        |
| timestamp | true | string | 时间戳       |
| signature | true | string | 签名         |

请求示例

``` javascript
/login?appid=123&p_user_id=1001&token=xxxxxxx&=timestamp=1592832321123&signature=asdfef2fsf22ef2sdfsg3grrg

```

返回值示例

``` javascript
验证成功直接登录商城，验证不成功直接页面提示登录失败

```

## 商城初始化生成兑换流水号

 接口功能

> 该接口用于获取商城初始化生成兑换流水号。

URL

> /api/v2/plat/points/exchange/init

HTTP请求方式

> POST

请求参数

| 参数                 | 必选       | 类型         | 说明                           |
| :------------------- | :--------- | :----------- | ------------------------------ |
| appid                | true       | string       | 应用id                         |
| p_user_id            | true       | string       | 平台用户标识                   |
| pay_points           | true       | string       | 兑换积分数量                   |
| ***pay_ext_points*** | ***true*** | ***string*** | ***兑换的附加积分（银币数）*** |
| timestamp            | true       | string       | 时间戳                         |
| signature            | true       | string       | 签名                           |

返回字段

<table>
  	<tr>
		<td>返回字段</td>
		<td>字段类型</td>
        <td colspan="2">说明</td>
	<tr>
	<tr>
		<td>code</td>
		<td>int</td>
        <td colspan="2">返回值编码(0:执行成功) </td>
	</tr>
    <tr>
		<td>msg</td>
		<td>string</td>
        <td colspan="2">返回值消息</td>
	</tr>
	<tr>
        <td rowspan="2">data</td>
        <td rowspan="2">json</td>
        <td>p_user_id</td>
        <td>平台用户标识</td>
	</tr>
    <tr>
        <td>order_number</td>
        <td>兑换流水号</td>
	</tr>
</table>

返回值示例

``` javascript
{
    "code": 0,
    "msg": "success",
    "data": {
        "p_user_id": "1001",
        "order_number":"OD1000001"
    }
}
```

## 通知商城用户兑换积分接口

接口功能

> 该接口用于通知商城用户兑换积分落地上商城用户。

URL

> /api/v2/plat/points/exchange/notify

HTTP请求方式

> POST

请求参数

| 参数                 | 必选       | 类型         | 说明                           |
| :------------------- | :--------- | :----------- | ------------------------------ |
| appid                | true       | string       | 应用id                         |
| p_user_id            | true       | string       | 平台用户标识                   |
| order_number         | true       | string       | 兑换流水号                     |
| pay_points           | true       | string       | 兑换积分(金币数)               |
| ***pay_ext_points*** | ***true*** | ***string*** | ***兑换的附加积分（银币数）*** |
| timestamp            | true       | string       | 时间戳                         |
| signature            | true       | string       | 签名                           |

返回字段

<table>
  	<tr>
		<td>返回字段</td>
		<td>字段类型</td>
        <td colspan="2">说明</td>
	<tr>
	<tr>
		<td>code</td>
		<td>int</td>
        <td colspan="2">返回值编码(0:执行成功) </td>
	</tr>
    <tr>
		<td>msg</td>
		<td>string</td>
        <td colspan="2">返回值消息</td>
	</tr>
	<tr>
        <td>data</td>
        <td>json</td>
        <td>is_deal</td>
        <td>1:处理成功;2:处理失败</td>
	</tr>
</table>

返回值示例

``` javascript
{
    "code": 0,
    "msg": "success",
    "data": {
        "is_deal":1
    }
}
```



## 积分兑换查询接口

接口功能

> 该接口用于积分对账和积分检测

URL

> /api/v2/plat/points/exchange/result/check

HTTP请求方式

> POST

请求参数

| 参数         | 必选 | 类型   | 说明             |
| :----------- | :--- | :----- | ---------------- |
| appid        | true | string | 应用id           |
| p_user_id    | true | string | 平台用户标识     |
| order_number | true | string | 兑换积分流水编号 |
| timestamp    | true | string | 时间戳           |
| signature    | true | string | 签名             |

返回字段

<table>
  	<tr>
		<td>返回字段</td>
		<td>字段类型</td>
        <td colspan="2">说明</td>
	<tr>
	<tr>
		<td>code</td>
		<td>int</td>
        <td colspan="2">返回值编码(0:执行成功) </td>
	</tr>
    <tr>
		<td>msg</td>
		<td>string</td>
        <td colspan="2">返回值消息</td>
	</tr>
	<tr>
        <td rowspan="4">data</td>
        <td rowspan="4">json</td>
        <td>order_number</td>
        <td>兑换积分流水编号</td>
	</tr>
	<tr>
        <td>pay_points</td>
        <td>积分数量（金币）</td>
    </tr>
    <tr>
        <td>pay_ext_points</td>
        <td>附加积分数量（银币）</td>
    </tr>
    <tr>
        <td>order_status</td>
        <td>兑换状态(0:未处理;1:处理中;2:处理完成;3:处理失败)</td>
    </tr>
    <tr>
        <td>create_time</td>
        <td>创建时间(时间戳)</td>
    </tr>
</table>

返回值示例

``` javascript
{
    "code": 0,
    "msg": "success",
    "data": {
        "order_number":"O_12434",
        "create_time":"1571415177",
        "pay_points":"202.00",
         "pay_ext_points":"30.00"  
        "status":"1"
    }
}
```

## 积分扣减初始化流水号接口

接口功能

> 该接口用于获取商城初始化生成积分扣减流水号。

URL

> /api/v2/plat/points/cut/init

HTTP请求方式

> POST

请求参数

| 参数                 | 必选       | 类型         | 说明                               |
| :------------------- | :--------- | :----------- | ---------------------------------- |
| appid                | true       | string       | 应用id                             |
| p_user_id            | true       | string       | 平台用户标识                       |
| cut_points           | true       | string       | 扣减的积分数量（金币数）           |
| ***cut_ext_points*** | ***true*** | ***string*** | ***扣减的附加积分数量（银币数）*** |
| timestamp            | true       | string       | 时间戳                             |
| signature            | true       | string       | 签名                               |

返回字段

<table>
  	<tr>
		<td>返回字段</td>
		<td>字段类型</td>
        <td colspan="2">说明</td>
	<tr>
	<tr>
		<td>code</td>
		<td>int</td>
        <td colspan="2">返回值编码(0:执行成功) </td>
	</tr>
    <tr>
		<td>msg</td>
		<td>string</td>
        <td colspan="2">返回值消息</td>
	</tr>
	<tr>
        <td rowspan="2">data</td>
        <td rowspan="2">json</td>
        <td>p_user_id</td>
        <td>平台用户标识</td>
	</tr>
    <tr>
        <td>cut_order_number</td>
        <td>扣减积分流水号</td>
	</tr>
</table>

返回值示例

``` javascript
{
    "code": 0,
    "msg": "success",
    "data": {
        "p_user_id": "1001",
        "cut_order_number":"KC1000001"
    }
}
```

## 通知商城用户积分扣减接口

接口功能

> 该接口用于通知商城用户积分扣减落地上商城用户。

URL

> /api/v2/plat/points/cut/notify

HTTP请求方式

> POST

请求参数

| 参数                 | 必选 | 类型   | 说明                               |
| :------------------- | :--- | :----- | ---------------------------------- |
| appid                | true | string | 应用id                             |
| p_user_id            | true | string | 平台用户标识                       |
| cut_order_number     | true | string | 兑换流水号                         |
| cut_points           | true | string | 扣减积分（金币数）                 |
| ***cut_ext_points*** | true | string | ***扣减的附加积分数量（银币数）*** |
| timestamp            | true | string | 时间戳                             |
| signature            | true | string | 签名                               |

返回字段

<table>
  	<tr>
		<td>返回字段</td>
		<td>字段类型</td>
        <td colspan="2">说明</td>
	<tr>
	<tr>
		<td>code</td>
		<td>int</td>
        <td colspan="2">返回值编码(0:执行成功) </td>
	</tr>
    <tr>
		<td>msg</td>
		<td>string</td>
        <td colspan="2">返回值消息</td>
	</tr>
	<tr>
        <td>data</td>
        <td>json</td>
        <td>is_deal</td>
        <td>1:处理成功;2:处理失败</td>
	</tr>
</table>

返回值示例

``` javascript
{
    "code": 0,
    "msg": "success",
    "data": {
        "is_deal":1
    }
}
```



## 订单结算接口

接口功能

> 该接口用于查询待发货或者已发货的订单信息。

URL

> /api/v2/plat/order/final

HTTP请求方式

> POST

请求参数

| 参数            | 必选 | 类型   | 说明                |
| :-------------- | :--- | :----- | ------------------- |
| appid           | true | string | 应用id              |
| done_time_start | true | string | 下单开始时间        |
| done_time_end   | true | string | 下单结束时间        |
| page_no         | true | string | 当前页(从1开始)     |
| page_size       | true | string | 每页条数(最大100条) |
| timestamp       | true | string | 时间戳              |
| signature       | true | string | 签名                |

返回字段

<table>
  	<tr>
		<td>返回字段</td>
		<td>字段类型</td>
        <td colspan="2">说明</td>
	<tr>
	<tr>
		<td>code</td>
		<td>int</td>
        <td colspan="2">返回值编码(0:执行成功) </td>
	</tr>
    <tr>
		<td>msg</td>
		<td>string</td>
        <td colspan="2">返回值消息</td>
	</tr>
	<tr>
        <td rowspan="11">data</td>
        <td rowspan="11">array</td>
        <td>shop_name</td>
        <td>商城名称</td>
	</tr>
    <tr>
        <td>supplier</td>
        <td>供货商</td>
	</tr>
    <tr>
        <td>main_order_id</td>
        <td>主订单编号</td>
	</tr>
    <tr>
        <td>order_id</td>
        <td>子订单编号</td>
	</tr>
    <tr>
        <td></td>
        <td>子订单名称(没意义)</td>
	</tr>
    <tr>
        <td>goods_num</td>
        <td>子订单数量(应该为商品数量)</td>
	</tr>
    <tr>
        <td>goods_name</td>
        <td>子订单商品名称</td>
	</tr>
    <tr>
        <td>goods_cost</td>
        <td>子订单商品成本</td>
	</tr>
    <tr>
        <td>pay_accounts</td>
        <td>结算金额</td>
	</tr>
    <tr>
        <td>done_time</td>
        <td>下单时间</td>
	</tr>
    <tr>
        <td>order_status</td>
        <td>子订单状态(代发货\已发货)</td>
	</tr>
</table>

返回值示例

``` javascript
{
    "code": 0,
    "msg": "success",
    "data": [{
            "shop_name":"商城名称1",
            "supplier":"供货商名称1",
            "main_order_id":"主订单编号1",
            "order_id":"子订单编号1",
            "goods_num":"订单商品数量1",
            "goods_name":"子订单商品名称1",
            "goods_cost":"子订单商品成本1",
            "pay_accounts":"结算金额1",
            "done_time":"下单时间1",
            "order_status":"子订单状态(代发货\已发货)1"
        },
        {
            "shop_name":"商城名称2",
            "supplier":"供货商名称2",
            "main_order_id":"主订单编号2",
            "order_id":"子订单编号2",
            "goods_num":"订单商品数量2",
            "goods_name":"子订单商品名称2",
            "goods_cost":"子订单商品成本2",
            "pay_accounts":"结算金额2",
            "done_time":"下单时间2",
            "order_status":"子订单状态(代发货\已发货)2"
        }
    ]
}
```

## 查询用户积分接口

接口功能

> 该接口用于查询用户积分信息详情。

URL

> /api/v2/plat/user/accounts

HTTP请求方式

> POST

请求参数

| 参数      | 必选 | 类型   | 说明       |
| :-------- | :--- | :----- | ---------- |
| appid     | ture | string | 应用id     |
| p_user_id | ture | string | 平台用户id |
| timestamp | ture | string | 时间戳     |
| signature | ture | string | 签名       |

返回字段

<table>
  	<tr>
		<td>返回字段</td>
		<td>字段类型</td>
        <td colspan="2">说明</td>
	<tr>
	<tr>
		<td>code</td>
		<td>int</td>
        <td colspan="2">返回值编码(0:执行成功) </td>
	</tr>
    <tr>
		<td>msg</td>
		<td>string</td>
        <td colspan="2">返回值消息</td>
	</tr>
<tr>
        <td rowspan="11">data</td>
        <td rowspan="11">json</td>
        <td>current_points</td>
        <td>持有积分（金币）</td>
</tr>
    <tr>
        <td>use_points</td>
        <td>消耗总积分（金币）</td>
	</tr>
    <tr>
        <td>back_points</td>
        <td>回退总积分（金币）</td>
	</tr>
         <tr>
        <td>current_ext_points</td>
        <td>持有附加总积分（银币）</td>
	</tr>
        <tr>
        <td>use_ext_points</td>
        <td>消耗附加总积分（银币）</td>
	</tr>
    <tr>
        <td>back_ext_points</td>
        <td>回退总附加积分（银币）</td>
	</tr>
</table>

返回值示例

``` javascript
{
    "code": 0,
    "msg": "success",
    "data": {
        "current_points": "100.00",
        "use_points": "105.00",
        "back_points": "10.00",
         "current_ext_points": "100.00",
        "use_ext_points": "105.00",
        "back_ext_points": "10.00",
            
    }
}
```

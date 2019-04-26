# 融安 Ronance API

- - - - - -

## API简介

欢迎使用Ronance API，API由两部分构成：[公共接口](#公共接口)和[私有接口](#私有接口)。

[公共接口](#公共接口)：可以查询行情、深度、成交记录等信息。

[私有接口](#私有接口)：可以交易、下单、撤单、查询委托等信息。

为了网络请求传输过程中的安全与防止个人数据被篡改，我们对于私有接口需要进行签名认证，保证我们的数据安全。步骤如下：

一、你需要向融安申请：

1、accesskey：访问秘钥，用于标识用户访问许可。

2、secrekey：公钥，用于参数签名。

3、nonce：用户会话信息保留。

>注意：向融安申请的accesskey、secrekey、nonce 不能被泄露，否则损失后果自负。

二、参数签名：

1、参数排序
>公共参数：accesskey、nonce、signature
>其中signature为签名参数，所以不参与参数签名。
>
>假设向融安申请的accesskey为:xxxxxx。secrekey为yyyyyy。nonce为zzzzzz。
>
>参与签名的参数进行排序：
>
>以下单为例，需要传的参数为：market、price、number、type、accesskey、nonce、signature
>其中signature 不参与签名。
>
>用&连接各参数：market=eth_usdt&price=200&number=10&type=1&accesskey=xxxxxx&nonce=zzzzzz（注意没有signature参数）
>
>对参数按字母排序：accesskey=xxxxxx&market=eth_usdt&nonce=zzzzzz&number=10&price=200&type=1

2、签名
>采用HmacMD5 对排序后的参数用secrekey进行签名
>
>签名后得到的字符串作为signature参数值
>
>例如签名之后得到的字符串为:aaaaaa。
>
>最终的请求参数为:accesskey=xxxxxx&market=eth_usdt&nonce=zzzzzz&number=10&price=200&type=1&signature=aaaaaa
>

----


公共接口：

* GET [/api/v1/market/ticker](#获取行情信息) 获取行情信息
* GET [/api/v1/order/history](#获取最新成交记录) 获取最新成交记录
* GET [/api/v1/market/depthData](#获取深度信息) 获取深度信息

私有接口：

* GET [/api/v1/account/balance](#获取账户资产信息) 获取账户资产信息
* GET [/api/v1/order/open](#获取委托订单) 获取委托订单
* GET [/api/v1/order/place](#下单) 下单
* GET [/api/v1/order/cancel](#撤单) 撤单

---

# 公共接口接口说明




## **获取行情信息**

* GET `/api/v1/market/ticker`

**请求参数**

| 参数        | 类型   |  是否必须   |  说明   |
| :--------:   | :-----:  |  :-----:  |  :-----:  |
| market        | string   |  √   |  市场名称   |

**返回值**

{"code":200,"data":{"market":"eth_usdt","tradeVol":"877345.17884","rate":"1.2200","price":"200.00000","ask":"389.83160","high":"444.00000","low":"0.01000","bid":"15.00000"},"info":"成功"}

**返回值说明**

| 返回值        |  说明   |
| :--------:   | :-----:  |
| price        |  最新价格   |
| market        |  市场   |
| tradeVol        |  24小时交易量   |
| rate        |  24小时涨幅   |
| ask        |  卖一   |
| bid        |  买一   |
| high        |  24小时最高价   |
| low        |  24小时最低价   |



----

## **获取最新成交记录**

* GET `/api/v1/order/history`

**请求参数**

| 参数        | 类型   |  是否必须   |  说明   |
| :--------:   | :-----:  |  :-----:  |  :-----:  |
| market        | string   |  √   |  市场名称   |
| since        | string   |  √   |  时间戳   |

**返回值**

{"code":200,"data":[{"quantity":512.00000,"price":1.00000,"time":1556199702080,"type":"bid"},....],"info":"成功"}

**返回值说明**

| 返回值        |  说明   |
| :--------:   | :-----:  |
| quantity        |  数量   |
| price        |  成交价格   |
| time        |  成交时间戳   |
| type        |  交易类型：bid 买；ask卖   |



----

## **获取深度信息**

* GET `/api/v1/market/depthData`

**请求参数**

| 参数        | 类型   |  是否必须   |  说明   |
| :--------:   | :-----:  |  :-----:  |  :-----:  |
| market        | string   |  √   |  市场名称   |
| depth        | int   |  √   |  深度   |

**返回值**

{"code":200,"data":{"market":"eth_usdt","last":200.00000,"rate":1.2200,"asks":[{"quantity":10.00000,"price":100.00000}],"bids":[{"quantity":1.00000,"price":90.00000},...}]},"info":"成功"}

**返回值说明**

| 返回值        |  说明   |
| :--------:   | :-----:  |
| price        |  价格   |
| quantity        |  数量   |




# 私有接口接口说明



## **获取账户资产信息**

* GET `/api/v1/account/balance`

**请求参数**

| 参数        | 类型   |  是否必须   |  说明   |
| :--------:   | :-----:  |  :-----:  |  :-----:  |
| accesskey         | string   |  √   |  访问秘钥   |
| nonce          | string   |  √   |  用户标识   |
| signature          | string   |  √   |  签名参数   |

**返回值**

{"code":200,"data":{"btc":{"freeze":"0.00","available":"10000.00"},"eth":{"freeze":"0.00","available":"107022.00"},.....},"info":"成功"}


**返回值说明**

| 返回值        |  说明   |
| :--------:   | :-----:  |
| frozen        |  冻结量   |
| available        |  可用量   |


----

## **获取委托订单**

* GET `/api/v1/order/open`

**请求参数**

| 参数        | 类型   |  是否必须   |  说明   |
| :--------:   | :-----:  |  :-----:  |  :-----:  |
| accesskey         | string   |  √   |  访问秘钥   |
| nonce          | string   |  √   |  用户标识   |
| signature          | string   |  √   |  签名参数   |
| market          | string   |  √   |  市场   |

**返回值**

{"code":200,"data":[{"number":"11.00000","price":"12.00000","id":26270,"time":1556265243000,"type":1,"status":0,"completeNumber":"0.00000"},...],"info":"成功"}

**返回值说明**

| 返回值        |  说明   |
| :--------:   | :-----:  |
| id        |  订单id   |
| price        |  挂单价   |
| status        |  订单状态，0:未成交 1:部分成交 2：结算中 3:已完成 4:已撤单  |
| number        |  挂单总量   |
| completeNumber        |  已成交量   |
| time        |  订单创建时间   |
| type        |  订单类型，0：卖 1：买   |


----

## **下单**

* POST `/api/v1/order/place`

**请求参数**

| 参数        | 类型   |  是否必须   |  说明   |
| :--------:   | :-----:  |  :-----:  |  :-----:  |
| accesskey         | string   |  √   |  访问秘钥   |
| nonce          | string   |  √   |  用户标识   |
| signature          | string   |  √   |  签名参数   |
| market          | string   |  √   |  市场   |
| price          | double   |  √   |  下单市价   |
| number          | double   |  √   |  下单数量   |
| type          | int   |  √   |  类型，0：卖 1：买   |

**返回值**

{"code":200,"data":{"id":26271},"info":"委托成功"}

**返回值说明**

| 返回值        |  说明   |
| :--------:   | :-----:  |
| id        |  订单id   |



----

## **撤单**

* DELETE `/api/v1/order/cancel`

**请求参数**

| 参数        | 类型   |  是否必须   |  说明   |
| :--------:   | :-----:  |  :-----:  |  :-----:  |
| accesskey         | string   |  √   |  访问秘钥   |
| nonce          | string   |  √   |  用户标识   |
| signature          | string   |  √   |  签名参数   |
| market          | string   |  √   |  市场   |
| id          | long   |  √   |  订单id   |


**返回值**

{"code":200,"info":"撤单成功"}





----


> #### 签名示例

下单请求参数：market=eth_usdt&price=10&number=100&type=1&accesskey=xxxxxx&nonce=zzzzzz

[注意没有signature参数（签名参数不参与签名）]
####
> #### 参数排序 

accesskey=xxxxxx&market=eth_usdt&nonce=zzzzzz&number=100&price=10&type=1

[按照ASCII码的顺序]

> ###### 签名 java demo


String content = "xxxxxx&market=eth_usdt&nonce=zzzzzz&number=100&price=10&type=1";
 
String secrekey = "3e491373-ceef-4e8f-ba31-e18085f01f20";

String signature = encryptHMAC(content,secrekey);


> ###### 请求链接

假设签名中得到签名参数signature=uuuuuu

则请求参数：

market=eth_usdt&price=10&number=100&type=1&accesskey=xxxxxx&signature=uuuuuu&nonce=aaaaaa

> ###### 签名方法 java demo
 
	public static String encryptHMAC(String conent, String key) {
		byte value[];

		try {
			value = conent.getBytes(encodingCharset);
		} catch (UnsupportedEncodingException e) {
			value = conent.getBytes();
		}
        SecretKey secretKey = new SecretKeySpec(key.getBytes(), "HmacMD5");
        Mac mac =null;
        try{
        	mac = Mac.getInstance(secretKey.getAlgorithm());
            mac.init(secretKey);
        }catch(Exception e){
        	e.printStackTrace();
        }
        return toHex(mac.doFinal(value));

    }

	public static String toHex(byte input[]) {

		if (input == null)
			return null;
		StringBuffer output = new StringBuffer(input.length * 2);
		for (int i = 0; i < input.length; i++) {
			int current = input[i] & 0xff;
			if (current < 16)
				output.append("0");
			output.append(Integer.toString(current, 16));
		}
		return output.toString();
	}


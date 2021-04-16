[TOC]

--------
# 字段要求说明
|标记|说明|
|--:--|--:--|
|Y|必填|
|N|选填|
|CY|条件必填|

# 请求方式
所有接口通过POST方式进行请求，请求头部需指定接收的数据类型为JSON。
即设置Content-Type: application/json;charset=utf-8
生产网址: http://www.mywow.com.cn/api/v2 + 接口地址
>如: 
投保接口生产接口为: /savePolicy
则投保的完整URL为: http://www.mywow.com.cn/api/v2/savePolicy

接口的请求参数均为：
|参数|数据类型|必填|说明|
|----|----|--:--|----|
|app_id|string|Y|APP ID由哇赛技术提供|
|data|string|Y|加密请求参数|
|timestamp|datetime|Y|发起请求的时间戳|
|sign|string|Y|签名，根据签名算法生成|
其中data的结构请查看所需要调用的具体接口说明，加密方式请参考加密请求参数。

# 签名算法
1、将所有的请求参数按照键值的首字母升序进行排列。
如参数值为数组对象，则数组各个元素需要按照首字母升序排列。
>例:
数组对象为
>```
array_obj = [
    {
        name: 'Name',
        gender: 'F',
        certificate_type: 1,
        certificate_no: '1234567890',
        mobile: '13900000000',
        email: '139000000@qq.com',
        relation_to_holder: 99,
        count: 1
    }, {
        name: 'Name',
        gender: 'F',
        certificate_type: 1,
        certificate_no: '1234567890',
        mobile: '13900000000',
        email: '139000000@qq.com',
        relation_to_holder: 99,
        count: 1
    }
];
>```
>则需要将数组内的对象的属性值按照首字母升序排列
>```
array_obj = [
    {
        certificate_no: '1234567890',
        certificate_type: 1,
        count: 1,
        email: '139000000@qq.com',
        gender: 'F',
        mobile: '13900000000',
        name: 'Name',
        relation_to_holder: 99
    }, {
        certificate_no: '1234567890',
        certificate_type: 1,
        count: 1,
        email: '139000000@qq.com',
        gender: 'F',
        mobile: '13900000000',
        name: 'Name',
        relation_to_holder: 99
    }
];
>```
如参数值为对象，则将对象的各个属性按照首字母升序排列。

2、将第一步处理好的请求参数转换成JSON格式，中文需转换成Unicode字符。（即：对原始内容进行处理）
3、将第二步生成的JSON字符串加上APP Secret拼接成待签字符串
```
sign_str = '{"app_id":"APP ID","data":"DATA","timestamp:"TIMESTAMP"}' + APP_SECRET
```
4、对待签字符串进行MD5计算，得到签名字符串
```
sign = md5(sign_str)
```

# 加密请求参数
本接口请求参数采用RSA 2048位加解密
接口请求及响应的参数中的data字段均采用加密传输，加密方式如下：
1、接入方向哇赛提供一个RSA2048位的公钥，用于加密响应的数据；
2、哇赛接入方提供一个RSA2048位的公钥，用于加密请求数据；
3、data字段的值按照签名算法第1、2步进行预处理，得到待加密字符串；
4、对待加密字符串使用哇赛的公钥进行加密，得到密文；
5、将密文转换成16进制编码，并转换为大写字母，得到最终的请求参数值。
>例:
原始内容:
>```
{"insurance":{"departure":"","destination":"","end_time":"","out_trade_no":"","plan_code":"","start_time":"","trip_mode":"","trip_no":""},"holder":{"birthday":"","certificate_no":"","certificate_type":"","email":"","gender":"","mobile":"","name":""},"insureds":[{"certificate_no":"","certificate_type":"","count":"","email":"","gender":"","mobile":"","name":"","relation_to_holder":""}]}
>```
加密得到:
>```
3D614B40211046A8A48C7EFE2A58B7538F2626090D814E3A9B81A19198A46820D0C926D664252D88A79FFE50BED1BAAC35DBA991BAE86056A0B553B86842DB1420B194A7DB12BB2C84409363A76695B4173440D422783BA6E0CA58BD34D896138694DC2A4AA082A63E774D85F1F8E0C6998312C60C8AE84DC11A0955866111E9C7492D3CBA118D6E64891658044BE692D889A63BC2C1DED1E9671E8A94251484F07E8B8005FC73D920FC4676E8C6C05BFBA041E62E4E003C79859A34BFD4FC176E54E1757995EBDF3BB9B2A98157BCBDC35AC49AA011CD99D4E45F202F631DE7203651A89EF638EF1A2D1908A789D2B6927B5D4BCB0E75F93680D14E826B90B75DE3C8D88F4EDC98FA25ADDAC49C8118CDDFD874F4E64BBC30E38E621D1F4D6114DDFA1240415E40676882CF11C7D1D83F2BEDE25C26DFBA5F442A25F744B85022C4951806FC9017EBA089B47FAF58C0C797B62975DCC6F43FE5CD66D6F379BE1E342612F8AD4BD10A1B492417F45282C419215C8383D7C0759B15DBC18A67ED1732A050D8DF6F9BE2131A15798CBAD514071F733B9C33671C7352C6283D8B55025F70F841D5AEE4A8B400561D6C8742977314088E15F6BC78CD8A045535CDC7B977A58498BBE9C581E714DFF87ABEDAFCC5AA15834C76BB280A6E04BF6F114E9FC02784EBA9FDE4FF46D6A1237D5B37407B9743C24BAC04DF62AB70825FA63C
>```

# 投保接口
生产接口: /savePolicy

data结构:
|参数|数据类型|必填|说明|
|----|----|--:--|----|
|insurance|Insurance|Y|保单信息|
|insureds|List&lt;Insured&gt;|Y|被保人信息列表|
|holder|Holder|Y|投保人信息|

Insurance结构:
|参数|数据类型|必填|说明|
|----|----|--:--|----|
|out_trade_no|string|Y|外部订单号，请保证全局唯一|
|plan_code|string|Y|保险产品代码，由哇赛技术提供|
|start_time|datetime|Y|保单生效期开始时间，YYYY-MM-DD HH:ii:ss格式|
|end_time|datetime|Y|保单生效期结束时间，YYYY-MM-DD HH:ii:ss格式|
|trip_mode|int|CY|出行方式：1-飞机，2-汽车/火车/高铁，3-船，99-其他 单次、1天保险需提供|
|trip_no|string|CY|航班号/车次号/船次号/其他 单次、1天保险需提供|
|destination|string|N|目的地|
|departure|string|N|出发地|
|e_ticket_no|string|CY|电子客票号，行程单保险产品该字段必填|

Insured结构:
|参数|数据类型|必填|说明|
|----|----|--:--|----|
|name|string|Y|被保险人姓名|
|gender|string|Y|被保险人性别：M-男，F-女|
|certificate_type|int|Y|证件类型：1-身份证，2-护照，3-其他|
|certificate_no|string|Y|证件号码|
|mobile|string|Y|被保险人手机号码|
|email|string|N|被保险人邮箱|
|birthday|datetime|Y|被保险人出生日期，YYYY-MM-DD格式，如：1980-01-01|
|relation_to_holder|int|Y|投保人与被保险人关系|
|count|int|Y|投保份数，请填写1|

Holder结构:
|参数|数据类型|必填|说明|
|----|----|--:--|----|
|name|string|Y|投保人姓名|
|gender|string|Y|投保人性别：M-男，F-女|
|certificate_type|string|Y|证件类型：1-身份证，2-护照，3-其他|
|certificate_no|string|Y|证件号码|
|birthday|datetime|Y|投保人出生日期，YYYY-MM-DD格式|
|email|string|N|投保人邮箱|
|mobile|string|Y|投保人手机号码|


返回参数:
|参数|数据类型|说明|
|----|----|----|
|code|int|全局响应状态码|
|message|string|全局响应信息|
|data|OrderInfo|订单信息|
|sign|string|签名|

OrderInfo:
|参数|数据类型|说明|
|----|----|----|
|order_sn|string|订单编号|
|policies|List&lt;Policy&gt;|保单列表|

Policy:
|参数|数据类型|说明|
|----|----|----|
|policy_no|string|保单号|
|status|int|保单状态: 1-待支付,2-已支付,3-已承保,6-撤保,7-投保失败,8-已完成|
|reason|string|失败原因，保单投保失败时将返回失败原因|
|certificate_type|int|被保人证件类型|
|certificate_no|string|被保人证件号码|

# 撤保接口
生产接口: /cancelPolicy

data结构:
|参数|数据类型|必填|说明|
|----|----|--:--|----|
|policies|List&lt;string&gt;|Y|保单号列表|

返回参数:
|参数|数据类型|说明|
|----|----|----|
|code|int|全局响应状态码|
|message|string|全局响应信息|
|data|List&lt;PolicyResult&gt;|订单信息|
|sign|string|签名|

PolicyResult:
|参数|数据类型|说明|
|----|----|----|
|policy_no|string|保单号|
|code|int|撤保响应状态|
|message|string|撤保响应信息|

# 电子保单下载接口
生产接口: /getPolicyPdf

data结构:
|参数|数据类型|必填|说明|
|----|----|--:--|----|
|policies|List&lt;string&gt;|Y|保单号列表|

返回参数:
|参数|数据类型|说明|
|----|----|----|
|code|int|全局响应状态码|
|message|string|全局响应信息|
|data|List&lt;PolicyPDF&gt;|订单信息|
|sign|string|签名|

PolicyPDF:
|参数|数据类型|说明|
|----|----|----|
|policy_no|string|保单号|
|url|string|保单URL地址|
|code|int|保单下载响应状态|
|message|string|保单下载响应信息|

# 证件类型
|代码|说明|
|--:--|--:--|
|1|身份证|
|2|护照|
|3|其他|

# 投保人与被保险人关系
|代码|说明|
|--:--|--:--|
|1|父子|
|2|母子|
|3|本人|
|4|配偶|
|5|子女|
|6|祖父|
|7|祖母|
|8|兄长|
|9|其他|

# 全局响应状态码
全局响应状态码不为200时，错误信息在message字段中返回，data字段为空数据。
>注意: 请求成功保单不一定投保成功，需要根据响应的保单状态进行判断
|状态码|说明|
|----|----|
|200|请求成功|
|550|没有访问权限|
|600|起保时间错误|
|601|终保时间错误|
|602|投保期间错误|
|700|出行凭证错误|
|701|行程单产品需提供电子客票号|
|800|用户余额不足|
|801|投保人信息错误|
|802|被保人信息错误|
|897|产品计划未开放|
|898|产品计划未分配|
|899|保险产品未找到|
|900|计划方案未找到或已下架|
|901|保单未找到|
|902|保单为无效状态|
|903|保单撤销失败|
|904|保单生成失败|
|905|投保起止日期不能早于当前日期|
|950|没有支持的支付方式|
|951|余额支付失败|
|952|信用支付失败|
|967|商家账户不存在|
|965|产品已停止供应|
|966|供应商暂停服务|
|969|接口已被停用|
|971|渠道不存在|
|972|签名验证失败|
|999|其它错误，message字段将返回具体提示信息|

# 撤保响应状态码
|状态码|说明|
|----|----|
|200|撤保成功|
|901|保单不存在|
|903|撤保失败|
|999|其它错误，message字段将返回具体提示信息|

# 保单下载响应状态码
|状态码|说明|
|----|----|
|200|撤保成功|
|901|保单不存在|
|902|保单为无效状态|
|905|保单下载失败|
|999|其它错误，message字段将返回具体提示信息|

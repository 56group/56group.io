---
title:  正则表达式
date: 2020-07-24 10:55:00
tags: [常用, 正则, CODE]
categories:  常用
---

# 正则表达式

##### 火车车次

```
/^[GCDZTSPKXLY1-9]\d{1,4}$/
```

##### 复制代码手机机身码(IMEI)

```
/^\d{15,17}$/
```

##### 复制代码必须带端口号的网址(或ip)

```
/^((ht|f)tps?:\/\/)?[\w-]+(\.[\w-]+)+:\d{1,5}\/?$/
```

##### 复制代码网址(url,支持端口和"?+参数"和"#+参数)

```
/^(((ht|f)tps?):\/\/)?[\w-]+(\.[\w-]+)+([\w.,@?^=%&:/~+#-]*[\w@?^=%&/~+#-])?$/
```

##### 复制代码统一社会信用代码

```
/^[0-9A-HJ-NPQRTUWXY]{2}\d{6}[0-9A-HJ-NPQRTUWXY]{10}$/
```

##### 复制代码迅雷链接

```
/^thunderx?:\/\/[a-zA-Z\d]+=$/
```

##### 复制代码ed2k链接(宽松匹配)

```
/^ed2k:\/\/\|file\|.+\|\/$/
```

##### 复制代码磁力链接(宽松匹配)

```
/^magnet:\?xt=urn:btih:[0-9a-fA-F]{40,}.*$/
```

##### 复制代码子网掩码

```
/^(?:\d{1,2}|1\d\d|2[0-4]\d|25[0-5])(?:\.(?:\d{1,2}|1\d\d|2[0-4]\d|25[0-5])){3}$/
```

##### 复制代码linux"隐藏文件"路径

```
/^\/(?:[^/]+\/)*\.[^/]*/
```

##### 复制代码linux文件夹路径

```
/^\/(?:[^/]+\/)*$/
```

##### 复制代码linux文件路径

```
/^\/(?:[^/]+\/)*[^/]+$/
```

##### 复制代码window"文件夹"路径

```
/^[a-zA-Z]:\\(?:\w+\\?)*$/
```

##### 复制代码window下"文件"路径

```
/^[a-zA-Z]:\\(?:\w+\\)*\w+\.\w+$/
```

##### 复制代码股票代码(A股)

```
/^(s[hz]|S[HZ])(000[\d]{3}|002[\d]{3}|300[\d]{3}|600[\d]{3}|60[\d]{4})$/
```

##### 复制代码大于等于0, 小于等于150, 支持小数位出现5, 如145.5, 用于判断考卷分数

```
/^150$|^(?:\d|[1-9]\d|1[0-4]\d)(?:.5)?$/
```

##### 复制代码html注释

```
/^<!--[\s\S]*?-->$/
```

##### 复制代码md5格式(32位)

```
/^([a-f\d]{32}|[A-F\d]{32})$/
```

##### 复制代码版本号(version)格式必须为X.Y.Z

```
^\d+(?:\.\d+){2}$/
```

##### 复制代码视频(video)链接地址（视频格式可按需增删）

```
/^https?:\/\/(.+\/)+.+(\.(swf|avi|flv|mpg|rm|mov|wav|asf|3gp|mkv|rmvb|mp4))$/i
```

##### 复制代码图片(image)链接地址（图片格式可按需增删）

```
/^https?:\/\/(.+\/)+.+(\.(gif|png|jpg|jpeg|webp|svg|psd|bmp|tif))$/i
```

##### 复制代码24小时制时间（HH:mm:ss）

```
/^(?:[01]\d|2[0-3]):[0-5]\d:[0-5]\d$/
```

##### 复制代码12小时制时间（hh:mm:ss）

```
/^(?:1[0-2]|0?[1-9]):[0-5]\d:[0-5]\d$/
```

##### 复制代码base64格式

```
/^\s*data:(?:[a-z]+\/[a-z0-9-+.]+(?:;[a-z-]+=[a-z0-9-]+)?)?(?:;base64)?,([a-z0-9!$&',()*+;=\-._~:@/?%\s]*?)\s*$/i
```

##### 复制代码数字/货币金额（支持负数、千分位分隔符）

```
/^-?\d+(,\d{3})*(\.\d{1,2})?$/
```

##### 复制代码数字/货币金额 (只支持正数、不支持校验千分位分隔符)

```
/(?:^[1-9]([0-9]+)?(?:\.[0-9]{1,2})?$)|(?:^(?:0){1}$)|(?:^[0-9]\.[0-9](?:[0-9])?$)/
```

##### 复制代码银行卡号（10到30位, 覆盖对公/私账户, 参考微信支付）

```
/^[1-9]\d{9,29}$/
```

##### 复制代码中文姓名

```
/^(?:[\u4e00-\u9fa5·]{2,16})$/
```

##### 复制代码英文姓名

```
/(^[a-zA-Z]{1}[a-zA-Z\s]{0,20}[a-zA-Z]{1}$)/
```

##### 复制代码车牌号(新能源)

```
/[京津沪渝冀豫云辽黑湘皖鲁新苏浙赣鄂桂甘晋蒙陕吉闽贵粤青藏川宁琼使领 A-Z]{1}[A-HJ-NP-Z]{1}(([0-9]{5}[DF])|([DF][A-HJ-NP-Z0-9][0-9]{4}))$/
```

##### 复制代码车牌号(非新能源)

```
/^(?:[京津沪渝冀豫云辽黑湘皖鲁新苏浙赣鄂桂甘晋蒙陕吉闽贵粤青藏川宁琼使领 A-Z]{1}[A-HJ-NP-Z]{1}(?:(?:[0-9]{5}[DF])|(?:[DF](?:[A-HJ-NP-Z0-9])[0-9]{4})))|(?:[京津沪渝冀豫云辽黑湘皖鲁新苏浙赣鄂桂甘晋蒙陕吉闽贵粤青藏川宁琼使领 A-Z]{1}[A-Z]{1}[A-HJ-NP-Z0-9]{4}[A-HJ-NP-Z0-9 挂学警港澳]{1})$/
```

##### 复制代码手机号(mobile phone)中国(严谨), 根据工信部2019年最新公布的手机号段

```
/^(?:(?:\+|00)86)?1(?:(?:3[\d])|(?:4[5-7|9])|(?:5[0-3|5-9])|(?:6[5-7])|(?:7[0-8])|(?:8[\d])|(?:9[1|8|9]))\d{8}$/
```

##### 复制代码手机号(mobile phone)中国(宽松), 只要是13,14,15,16,17,18,19开头即可

```
/^(?:(?:\+|00)86)?1[3-9]\d{9}$/
```

##### 复制代码手机号(mobile phone)中国(最宽松), 只要是1开头即可, 如果你的手机号是用来接收短信, 优先建议选择这一条

```
/^(?:(?:\+|00)86)?1\d{10}$/
```

##### 复制代码date(日期)

```
/^\d{4}(-)(1[0-2]|0?\d)\1([0-2]\d|\d|30|31)$/
```

##### 复制代码email(邮箱)

```
/^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
```

##### 复制代码座机(tel phone)电话(国内),如: 0341-86091234

```
/^\d{3}-\d{8}$|^\d{4}-\d{7,8}$/
```

##### 复制代码身份证号(1代,15位数字)

```
/^[1-9]\d{7}(?:0\d|10|11|12)(?:0[1-9]|[1-2][\d]|30|31)\d{3}$/
```

##### 复制代码身份证号(2代,18位数字),最后一位是校验位,可能为数字或字符X

```
/^[1-9]\d{5}(?:18|19|20)\d{2}(?:0[1-9]|10|11|12)(?:0[1-9]|[1-2]\d|30|31)\d{3}[\dXx]$/
```

##### 复制代码身份证号, 支持1/2代(15位/18位数字)

```
/(^\d{8}(0\d|10|11|12)([0-2]\d|30|31)\d{3}$)|(^\d{6}(18|19|20)\d{2}(0[1-9]|10|11|12)([0-2]\d|30|31)\d{3}(\d|X|x)$)/
```

##### 复制代码护照（包含香港、澳门）

```
/(^[EeKkGgDdSsPpHh]\d{8}$)|(^(([Ee][a-fA-F])|([DdSsPp][Ee])|([Kk][Jj])|([Mm][Aa])|(1[45]))\d{7}$)/
```

##### 复制代码帐号是否合法(字母开头，允许5-16字节，允许字母数字下划线组合

```
/^[a-zA-Z]\w{4,15}$/
```

##### 复制代码中文/汉字

```
/^(?:[\u3400-\u4DB5\u4E00-\u9FEA\uFA0E\uFA0F\uFA11\uFA13\uFA14\uFA1F\uFA21\uFA23\uFA24\uFA27-\uFA29]|[\uD840-\uD868\uD86A-\uD86C\uD86F-\uD872\uD874-\uD879][\uDC00-\uDFFF]|\uD869[\uDC00-\uDED6\uDF00-\uDFFF]|\uD86D[\uDC00-\uDF34\uDF40-\uDFFF]|\uD86E[\uDC00-\uDC1D\uDC20-\uDFFF]|\uD873[\uDC00-\uDEA1\uDEB0-\uDFFF]|\uD87A[\uDC00-\uDFE0])+$/
```

##### 复制代码小数

```
/^\d+\.\d+$/
```

##### 复制代码数字

```
/^\d{1,}$/
```

##### 复制代码html标签(宽松匹配)

```
/<(\w+)[^>]*>(.*?<\/\1>)?/
```

##### 复制代码qq号格式正确

```
/^[1-9][0-9]{4,10}$/
```

##### ### 复制代码数字和字母组成

```
/^[A-Za-z0-9]+$/
```

##### 复制代码英文字母

```
/^[a-zA-Z]+$/
```

##### 复制代码小写英文字母组成

```
/^[a-z]+$/
```

##### 复制代码大写英文字母

```
/^[A-Z]+$/
```

##### 复制代码密码强度校验，最少6位，包括至少1个大写字母，1个小写字母，1个数字，1个特殊字符

```
/^\S*(?=\S{6,})(?=\S*\d)(?=\S*[A-Z])(?=\S*[a-z])(?=\S*[!@#$%^&*? ])\S*$/
```

##### 复制代码用户名校验，4到16位（字母，数字，下划线，减号）

```
/^[a-zA-Z0-9_-]{4,16}$/
```

##### 复制代码ip-v4

```
/^(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/
```

##### 复制代码ip-v6

```
/^((([0-9A-Fa-f]{1,4}:){7}[0-9A-Fa-f]{1,4})|(([0-9A-Fa-f]{1,4}:){6}:[0-9A-Fa-f]{1,4})|(([0-9A-Fa-f]{1,4}:){5}:([0-9A-Fa-f]{1,4}:)?[0-9A-Fa-f]{1,4})|(([0-9A-Fa-f]{1,4}:){4}:([0-9A-Fa-f]{1,4}:){0,2}[0-9A-Fa-f]{1,4})|(([0-9A-Fa-f]{1,4}:){3}:([0-9A-Fa-f]{1,4}:){0,3}[0-9A-Fa-f]{1,4})|(([0-9A-Fa-f]{1,4}:){2}:([0-9A-Fa-f]{1,4}:){0,4}[0-9A-Fa-f]{1,4})|(([0-9A-Fa-f]{1,4}:){6}((\b((25[0-5])|(1\d{2})|(2[0-4]\d)|(\d{1,2}))\b)\.){3}(\b((25[0-5])|(1\d{2})|(2[0-4]\d)|(\d{1,2}))\b))|(([0-9A-Fa-f]{1,4}:){0,5}:((\b((25[0-5])|(1\d{2})|(2[0-4]\d)|(\d{1,2}))\b)\.){3}(\b((25[0-5])|(1\d{2})|(2[0-4]\d)|(\d{1,2}))\b))|(::([0-9A-Fa-f]{1,4}:){0,5}((\b((25[0-5])|(1\d{2})|(2[0-4]\d)|(\d{1,2}))\b)\.){3}(\b((25[0-5])|(1\d{2})|(2[0-4]\d)|(\d{1,2}))\b))|([0-9A-Fa-f]{1,4}::([0-9A-Fa-f]{1,4}:){0,5}[0-9A-Fa-f]{1,4})|(::([0-9A-Fa-f]{1,4}:){0,6}[0-9A-Fa-f]{1,4})|(([0-9A-Fa-f]{1,4}:){1,7}:))$/i
```

##### 复制代码16进制颜色

```
/^#?([a-fA-F0-9]{6}|[a-fA-F0-9]{3})$/
```

##### 复制代码微信号(wx)，6至20位，以字母开头，字母，数字，减号，下划线

```
/^[a-zA-Z][-_a-zA-Z0-9]{5,19}$/
```

##### 复制代码邮政编码(中国)

```
/^(0[1-7]|1[0-356]|2[0-7]|3[0-6]|4[0-7]|5[1-7]|6[1-7]|7[0-5]|8[013-6])\d{4}$/
```

##### 复制代码中文和数字

```
/^((?:[\u3400-\u4DB5\u4E00-\u9FEA\uFA0E\uFA0F\uFA11\uFA13\uFA14\uFA1F\uFA21\uFA23\uFA24\uFA27-\uFA29]|[\uD840-\uD868\uD86A-\uD86C\uD86F-\uD872\uD874-\uD879][\uDC00-\uDFFF]|\uD869[\uDC00-\uDED6\uDF00-\uDFFF]|\uD86D[\uDC00-\uDF34\uDF40-\uDFFF]|\uD86E[\uDC00-\uDC1D\uDC20-\uDFFF]|\uD873[\uDC00-\uDEA1\uDEB0-\uDFFF]|\uD87A[\uDC00-\uDFE0])|(\d))+$/
```

##### 复制代码不能包含字母

```
/^[^A-Za-z]*$/
```

##### 复制代码java包名

```
/^([a-zA-Z_][a-zA-Z0-9_]*)+([.][a-zA-Z_][a-zA-Z0-9_]*)+$/
```

##### 复制代码mac地址

```
/^((([a-f0-9]{2}:){5})|(([a-f0-9]{2}-){5}))[a-f0-9]{2}$/i
```

##### 复制代码匹配连续重复的字符

```
/(.)\1+/
```
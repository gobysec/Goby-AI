# Goby AI 智能生成目录遍历类型漏洞的PoC/EXP步骤及其构成方式

以“RaidenMAILD Mail Server /webeditor/ 目录遍历漏洞（CVE-2024-32399）”为例进行讲解。

我们可以先查看此漏洞细节的参考来源链接：

https://cve.imfht.com/poc_detail/4646c1bfc74e22ad244ba4fda8a1ad86509cec89

首先我们需要知道的是，在Goby PoC的JSON图形化页面中,一个完整的PoC分为三个模块，分别为：漏洞信息、扫描测试、利用测试。

可以看到，来源文章中包含了漏洞描述、Request（请求包）、Response（响应包）

当接收到文章链接或文本时，目录遍历智能体首先解析文章中的文本和图片内容，提取漏洞背景描述、请求包和响应包等关键信息。

若文章中缺少对应模块信息，则会对解析文本内容智能补充。

## 提取漏洞基础信息：

在漏洞信息模块，包含的信息字段有：漏洞名称、查询规则、等级、漏洞描述、解决方案、漏洞类型（Tag)、CVE编号等信息。

智能体提取文本中的 产品名、漏洞路径、漏洞类型、CVE编号等信息，自动以 “产品名称+漏洞路径+漏洞类型+(CVE编号)"的格式生成漏洞名称

1.智能生成漏洞名称：RaidenMAILD Mail Server /webeditor/ 目录遍历漏洞（CVE-2024-32399）；

2.参考来源中由于缺少规则指纹信息，智能体根据产品名称（RaidenMAILD-Mail-Server) 智能匹配FOFA app规则：app="RaidenMAILD-Mail-Server"；

3.根据页面内容智能分析漏洞描述、影响版本、解决方案等信息。

同时自动翻译英文版自动填充：

![](https://s3.bmp.ovh/imgs/2024/12/18/e653f74eb456bec8.png)

![](https://s3.bmp.ovh/imgs/2024/12/18/0452678addcea5d8.png)



需要注意的是：

由智能体自动补充的语法信息，规则语法虽然不会影响Poc/EXP检测，但是若需要通过资产扫描匹配漏洞，可能会出现资产规则和漏洞资产不匹配不触发PoC发包检测的情况。建议进行二次人工校准，填写更为准确的语法。




## 提取扫描测试请求包及响应包验证条件：

智能体基于提取的请求包信息，自动生成符合 Goby 格式的 JSON 代码，并从响应包中识别状态码及具有代表性的字符串，作为PoC扫描验证条件。

自动填写请求包：

Request: 

GET /webeditor/../../../windows/win.ini 

HTTP/1.1 Host: xx.xx.xx.xx 

Cache-Control: max-age=0 

Connection: close

![](https://s3.bmp.ovh/imgs/2024/12/18/981782c53ff1a708.png)

自动填写响应包：

提取特征信息，若响应包中存在特征则证明漏洞存在。

![](https://s3.bmp.ovh/imgs/2024/12/18/41f7734e66162695.png)

![](https://s3.bmp.ovh/imgs/2024/12/18/98cb8ef7afe5bffa.png)




## 定义利用测试输入方式及输出结果：

利用输入（EXP Params）：

智能体提取的文件访问路径将自动填入 EXP 参数中。

验证方式支持两种选择：

● 默认方式（default)，直接使用文章中已有的访问路径；

● 用户自定义方式(custom)，允许用户指定输出的访问路径以满足特定需求。

![](https://s3.bmp.ovh/imgs/2024/12/18/04fb5d7a444c3931.png)

同时，智能体会将访问文件路径替换为变量，并填充至 EXP Params 中，方便用户查看和自定义输入访问路径。

![](https://s3.bmp.ovh/imgs/2024/12/18/be115bfa30620058.png)



****

利用输出（output）：

智能体通过正则表达式，提取响应包中lastbody中的所有内容进行输出。

![](https://s3.bmp.ovh/imgs/2024/12/18/51f31ca031473ad8.png)


# 实战检验: RaidenMAILD Mail Server /webeditor/ 目录遍历漏洞（CVE-2024-32399）

输入靶场目标进行扫描，确认PoC扫描测试准确性：√

![](https://s3.bmp.ovh/imgs/2024/12/18/e696db82db04eedd.png)

点击验证，检测EXP输出信息的准确性：√
![](https://s3.bmp.ovh/imgs/2024/12/18/2a689c233744543a.png)


## GobyAI 智能生成未授权访问类型漏洞完整流程演示效果如下：
![](https://s3.bmp.ovh/imgs/2024/12/18/0a8a715b1419fbbb.gif)




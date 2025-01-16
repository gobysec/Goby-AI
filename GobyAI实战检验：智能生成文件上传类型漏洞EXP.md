# Goby AI 智能生成文件上传类型漏洞的PoC/EXP步骤及其构成方式

以“PowerCreatorCMS UploadResourcePic 任意文件上传漏洞”为例，我们可以先查看此漏洞细节的参考来源链接：

[PowerCreatorCMS UploadResourcePic 任意文件上传漏洞](https://blog.csdn.net/qq_41904294/article/details/140372781?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522249c54035077b93852884a9e36912115%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=249c54035077b93852884a9e36912115&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-2-140372781-null-null.nonecase&utm_term=%E6%96%87%E4%BB%B6%E4%B8%8A%E4%BC%A0&spm=1018.2226.3001.4450)

可以看到，来源链接中，包含 漏洞名称、产品简介、漏洞概述、复现环境、漏洞复现、修复建议

在JSON图形化页面中,Goby PoC自定义编写模板分为三个模块，分别为：漏洞信息、扫描测试（PoC）、利用测试(EXP)，AI需要针对这三个模块进行智能填充


首先，当接收到漏洞详情文章链接或文本时，智能体首先解析文章中的文本和图片内容，提取漏洞背景描述、漏洞类型、资产语法、请求包和响应包等关键信息。

## 提取漏洞基础信息：
漏洞名称：PowerCreatorCMS UploadResourcePic 任意文件上传漏洞
漏洞类型：文件上传
漏洞简介：PowerCreator CMS是翰博尔信息技术有限公司（简称翰博尔PowerCreator）推出的一款教育资源管理平台，专注于教育领域的信息化解决方案。PowerCreator CMS是集成了软件平台和硬件设备、多系统高度融合的教育资源管理平台。它旨在通过技术手段提升教育资源的管理、共享和应用效率，助力教育机构实现教学管理的现代化和智能化。PowerCreatorCMS UploadResourcePic 接口存在任意文件上传漏洞，未经身份攻击者可通过该漏洞在服务器端任意执行代码，写入后门，获取服务器权限，进而控制整个 web 服务器。

FOFA语句：app="PowerCreator-CMS"

智能体提取到这部分信息进行自动填写，同时自动翻译英文版自动填充：
**![](https://s3.bmp.ovh/imgs/2025/01/16/ace69fa618686252.png)**

**![](https://s3.bmp.ovh/imgs/2025/01/16/4da10f2054416bc8.png)**


## 提取扫描测试请求包及响应包验证条件：

智能体扫描文章提取到文件上传类型为“aspx”，并定义全局变量“SCRIPT_TYPE”来控制文件上传的类型。（SCRIPT_TYPE为Goby规约的的内置变量，用于定义文件上传的文件类型）

**![](https://s3.bmp.ovh/imgs/2025/01/16/9b9f36d10148c0dc.png)**


扫描测试请求包：
智能体提取文章中的请求包信息，并填入框内
**![](https://s3.bmp.ovh/imgs/2025/01/16/c5be73e85d296198.png)**

其中{{{CONTENT}}}是Goby文件上传PoC模板中的一个内置变量，表示文件内容，内容的类型会根据变量“SCRIPT_TYPE”来选择内置的aspx类型上传码。

响应包验证条件：
与其他通过响应包特则来检测的漏洞逻辑不同，文件上传类型的检测逻辑是：构造文件内容（选择对应类型的检测模板木码）→上传文件→获取文件路径（在案例漏洞中因为访问路径不变，所以不需要再获取）→访问文件（访问后自动删除文件）→检测返回信息（检测返回的内容与goby本地相对应的内容是否相同，相同则存在漏洞，反之则不存在。）

在Goby内置的文件上传漏洞模板中，通过Goby的内置参数及模板自动实现上述检测流程，所以在AI转化的响应包验证条件中仅填入 “code=200”即可。

**![](https://s3.bmp.ovh/imgs/2025/01/16/7c5d60540e7c2bf5.png)**

**![](https://s3.bmp.ovh/imgs/2025/01/16/142fd1a28c8bcf12.png)**


## 定义利用测试输入方式及输出结果：

输入：
智能体识别出该文章是文件上传类型时，就会选择相对应的exp参数自动填写：
**![](https://s3.bmp.ovh/imgs/2025/01/16/310705fba5f22874.png)**

其中exp有ShellHub,Godzilla,Command,Customize几种模式：
ShellHub指获取shell
Godzilla表示哥斯拉连接信息
command指命令执行
Customize指自定义文件内容。

输出：
通过Goby内置模板自动实现利用测试，所以文件上传类型漏洞无需再进行利用测试模块的编写。（在后续发布的正式版中，会将此模板开放编写，当前内测版暂不开放）

智能体提取到文中上传文件的访问路径后，会定义一个自定义变量“FILE_PATH”来控制上传文件后的访问路径。（“FILE_PATH”也是属于文件上传模板中的一个内置变量，表示文件上传的访问路径）
**![](https://s3.bmp.ovh/imgs/2025/01/16/ffcc7083a1020d26.png)**


# 实战检验: PowerCreatorCMS UploadResourcePic 任意文件上传漏洞

输入靶场目标进行扫描，确认扫描测试准确性：√

**![](https://s3.bmp.ovh/imgs/2025/01/16/d3186e4999bee37a.png)**

点击验证，检测输出信息的准确性：√
**![](https://s3.bmp.ovh/imgs/2025/01/16/609a640200e07ef3.png)**


## GobyAI 智能生成文件上传类型漏洞完整流程演示效果如下：

**![](https://s3.bmp.ovh/imgs/2025/01/16/7c019129ed96bfcc.gif)**

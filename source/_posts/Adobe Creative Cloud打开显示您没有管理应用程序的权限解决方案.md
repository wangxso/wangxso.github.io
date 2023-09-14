title: Adobe Creative Cloud打开显示您没有管理应用程序的权限解决方案
date: 2023-03-05 12:57:00
categories: zczb
tags: []
---
**今天安装了Adobe Creative Cloud突然发现和前次在Mac上安装出现了一样的错误。所以就自己写个怎么解决的方案吧。**

原网站: https://www.mac69.com/news/2123.html

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/03/4228446856.png)

## MacOS
- 点击访达【前往】【前往文件夹】，或者使用
`shift+command+g`快捷键，如图：
![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/03/2917999684.png)

- 前往`/Library/Application Support/Adobe/OOBE/Configs/`路径，如图：
![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/03/2807200452.png)
- 找到`ServiceConfig.xml`文件，复制`ServiceConfig.xml`文件到桌面，使用文本编辑打开，如图：
![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/03/3609786159.png)
- 此时第一行`visiable`内容显示为`false`，如图：
![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/03/3435026113.png)
- 将`false`更改为`true`然后保存，如图：
![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/03/1880148662.png)
之后重新登录creative cloud账号即可正常使用！

# Windows
- 打开资源管理器，在上方地址栏输入`C:\Program Files (x86)\Common Files\Adobe\OOBE\Configs`

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/03/3715890375.png)

- 选择用记事本打开`ServiceConfig.xml`,此时visible的值为`false`
![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/03/3696892130.png)
- 将它改为`true`保存
![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/03/1091517381.png)

之后重新登录creative cloud账号即可正常使用！
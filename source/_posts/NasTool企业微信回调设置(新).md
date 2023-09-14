title: NasTool企业微信回调设置(新)
date: 2023-01-16 04:22:00
categories: zczb
tags: []
---
# 1 创建企业微信应用

- step1 进入链接https://work.weixin.qq.com/wework_admin/frame#apps

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/01/4227550833.png)

- step 2 输入相关信息

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/01/3631833767.png)

- step3 记住AgentId和Secret


![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/01/2799206703.png)

- step4 获取企业ID

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/01/2584071887.png)

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/01/31056227.png)



- step5 回到nastool填写信息

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/01/3376161872.png)

> 填写企业ID、Secret和应用ID，消息推送代理下面在填写

# 2. 消息推送&回调设置

- 点击进入

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/01/1655452374.png)

- 点击设置API

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/01/525685936.png)

- 设置API，这里先别保存，先把相关信息填到nastool里面点击保存后再点企业微信这边的保存，不然不通过的。**注意第一个地址是你本机nastool所在的地址，并且你要有公网IP进行ddns或者你用反向代理FRP之类的也行。**

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/01/2932680131.png)



- 最后对于**消息推送代理**的设置，由于企业微信更新了规则，所以以前作者提供的代理服务器不行了，所以要自行搭建一下。

  - 找一台有公网IP的服务器，如云服务商的服务器，我用的是阿里云的。

  - 如果你用的Nginx，那么就在你所对应网站的Nginx配置文件中加入

    ```ng
    location /cgi-bin/gettoken {
      proxy_pass https://qyapi.weixin.qq.com;
    }
    location /cgi-bin/message/send {
      proxy_pass https://qyapi.weixin.qq.com; 
    }
    ```

  - 如果你用的是Caddy搭建代理服务(（`{upstream_hostport}` 部分不是变量，不要改，原封不动复制粘贴过去即可）)

    ```
    reverse_proxy https://qyapi.weixin.qq.com {
      header_up Host {upstream_hostport}
    }
    ```

  - 然后在你的nastool配置中的消息推送代理，填入你Nginx的地址如 http://nastool.example.com 即可

# 3. 配置微信菜单控制

- 配置微信菜单控制 通过菜单远程控制工具运行，在https://work.weixin.qq.com/wework_admin/frame#apps 应用自定义菜单页面按如下图所示维护好菜单，菜单内容为发送消息，消息内容随意。**一级菜单及一级菜单下的前几个子菜单顺序需要一模一样**，在符合截图的示例项后可以自己增加别的二级菜单项。

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/01/2959344127.png)

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/01/3174108928.png)
title: ROS2装尝试
date: 2023-07-07 08:07:00
categories: selfdriving
tags: []
---
# ROS2

1. ~~关闭SIP~~
    1. ~~关机~~
    2. ~~按住开机键10秒，知道出现选择启动项界面，选择设置~~
    3. ~~选择终端~~
    
    ```bash
    csrutil disable
    ```
    
    ~~然后重启电脑即可。~~
    
2. ~~M1的Macbook Air下的MacOS 13.4.1 Venture版本出现错误, 执行~~ 
    
    ```bash
    colcon build --symlink-install --packages-skip-by-dep python_qt_binding
    ```
    

      ~~发生了错误~~

```bash
--- stderr: rviz_ogre_vendor
In file included from /Users/wangxs/ros2_iron/build/rviz_ogre_vendor/ogre-v1.12.10-prefix/src/ogre-v1.12.10/OgreMain/src/OgreASTCCodec.cpp:29:
In file included from /Users/wangxs/ros2_iron/build/rviz_ogre_vendor/ogre-v1.12.10-prefix/src/ogre-v1.12.10/OgreMain/src/OgreStableHeaders.h:45:
In file included from /Users/wangxs/ros2_iron/build/rviz_ogre_vendor/ogre-v1.12.10-prefix/src/ogre-v1.12.10/OgreMain/include/OgrePrerequisites.h:35:
/Users/wangxs/ros2_iron/build/rviz_ogre_vendor/ogre-v1.12.10-prefix/src/ogre-v1.12.10/OgreMain/include/OgrePlatform.h:183:13: fatal error: 'Availability.h' file not found
#   include "Availability.h"
            ^~~~~~~~~~~~~~~~
1 error generated.
```

~~我在网上搜了原因和解决方法都无果，比如~~

[https://github.com/ros2/ros2/issues/948](https://github.com/ros2/ros2/issues/948)

~~我的猜想可能跟xcode版本有关，旧版本的xcode可能会带有该头文件，并且可能我的版本太新了导致的，随即不折腾了~~。

1. Docker启动(曲线救国)
    1. 拉取Ubuntu22.04镜像
        
        ```bash
        docker pull ubuntu:22.04
        ```
        
    2. 运行容器 
        
        ```bash
        docker run -itd --name ubuntu-test -v /yourpath:/mnt ubuntu:22.04 
        ```
        
    3. 运行命令进入容器
        
        ```bash
        docker exec -it ubuntu-test /bin/bash
        ```
        
    4. 根据ros2文档进行安装
        
        [ROS2 Installation For Ubuntu](https://docs.ros.org/en/iron/Installation/Alternatives/Ubuntu-Development-Setup.html#)
        

遇到了坑点，因为docker没有图形界面，所以导致turtlesim的样例无法启动。解决办法还是有的就是想我之前安装**mininet**一样。

[如何让mininet在M1系列的Macbook上运行？ - Wangxs的博客](https://blog.wangx.wang/index.php/archives/181/)

但是我这是虚拟机的解决方案？不会让我装在虚拟机里吧，emm。算了暂时不搞哪个样例了。
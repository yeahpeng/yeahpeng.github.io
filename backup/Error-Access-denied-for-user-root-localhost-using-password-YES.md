title: "Error:Access denied for user 'root'@'localhost' (using password: YES)"
date: 2015-12-04 17:48:32
tags: error
categories: work
---
　　事实证明报：
>Error:Access denied for user 'root'@'localhost' (using password: YES)

　　不一定是数据库密码错误。<!-- more -->
　　昨晚在启动项目时，出现上述错误，下意识以为密码错了，尝试直接通过`控制台`却成功登陆。在网上查了许久，发现可能是`mysql`的jar包没加入路径。
　　解决很简单,`build path`中添加即可。
　　</br>
###**总结一下：**
　　1. 可能是数据库密码错误(可能性最大)
　　2. `mysql`的jar包没加入路径

<center>![work](http://7xl69p.com1.z0.glb.clouddn.com/work码农.png)</center>
## 介绍
`dde-session-shell`是UOS用于管理用户登录和锁屏，关机等操作的一个项目，其功能分为两个可执行程序：`dde-lock`和`lightdm-deepin-greeter`,其中`dde-lock`主要用于管理锁屏，关机等操作，`lightdm-deepin-greeter`用于管理用户登录操作。
## dde-session-shell整体框架
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1202325/1625559487951-36c721ff-3069-4d45-82c6-40c26522084b.png#clientId=ue7859fc6-0548-4&from=paste&height=484&id=u61148b34&margin=%5Bobject%20Object%5D&name=image.png&originHeight=967&originWidth=1848&originalType=binary&ratio=1&size=162452&status=done&style=none&taskId=u91dda2b8-8c9e-4570-945b-43288d2e2ba&width=924)


## dde-lock逻辑框架
待完善。
## lightdm-deepin-greeter逻辑框架
待完善。
## dde-session-shell认证框架
认证逻辑主要在lockworker.cpp, greeterworkek.cpp和deepinauthframework.cpp这三个文件中，基本认证调用关系如下：
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1202325/1626944625662-f0ec4064-6a10-4f95-a0bc-b948a1598553.png#clientId=u7f63e5fb-2e7f-4&from=paste&height=809&id=u0b5718a0&margin=%5Bobject%20Object%5D&name=image.png&originHeight=809&originWidth=935&originalType=binary&ratio=1&size=83893&status=done&style=none&taskId=u548834a3-0b15-4ea0-84df-3f6bf3b378f&width=935)
### dde-lock认证框架
`dde-lock`认证逻辑主要在lockwork.cpp这个文件里面，主要通过调用后端或系统服务提供的`D-Bus`接口或监听对应的信号去做相应的逻辑处理。
​


- ​`LockWorker::LockWorker`: 初始化相应数据，并根据后端或者相应服务改变信号去作对应的逻辑处理，主要有以下：
- ​`m_accountsInter`: 后端`com.deepin.daemon.Accounts`的一个指针，通过监听后端的`UserAdded`、`UserDeleted`、`UserListChanged`等信号去做相应的逻辑处理；
- ​`m_loginedInter`: 后端`com.deepin.daemon.Accounts`下`/com/deepin/daemon/Logined`服务的一个指针，主要监听用户登录的相关信号做对应的逻辑处理;
- `m_sessionManagerInter`: 系统服务`com.deepin.SessionManager`, 用户`session`管理；
- `m_lockInter`: 后端`com.deepin.dde.LockService`服务，主要提供切换用户的相关操作；
- `m_xEventInter`: 后端`dde-session-daemon`提供的服务，这里主要有用到监听鼠标事件处理；
### lightdm-deepin-greeter认证框架
`lightdm-deepin-greeter`认证逻辑和`dde-lock`大体相似，只不过在登录时增加了`QLightDM::Greeter`认证。
## dde-session-shell调试及问题定位
### 调试
`dde-session-shell`当前可通过`Qt`进行调试，其中`lightdm-deepin-greeter`可在桌面打断点的方式进行调试，而`dde-lock`则只能通过加日志的方式进行问题调试。
​

### Log和日志分析

1. `dde-lock`的日志在用户目录下的`.cache/deepin/dde-lock`下，调试时我们可通过`tail -f ~/.cache/deepin/dde-lock/dde-lock.log`实时查看日志，进行问题定位和分析；
1. `lightdm-deepin-greeter`的日志在目录`/var/log/lightdm`下，其中`lightdm.log`为`lightdm`日志(`.old`结尾的为历史日志), `seat0-greeter.log`为`lightdm-deepin-greeter`日志， 剩下几个是`xorg`的日志；
1. 因为登录界面涉及到认证，所以当有认证问题时，我们还需要结合`/var/log/auth.log`日志进行分析定位。

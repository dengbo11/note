
[toc]

<a name="50841f34"></a>
# Linux 开源PAM模块学习


<a name="a4d3b02a"></a>
## 概述

<br />PAM（Pluggable Authentication Modules ）是由Sun提出的一种认证机制。它通过提供一些动态链接库和一套统一的API，将系统提供的服务和该服务的认证方式分开，使得系统管理员可以灵活地根据需要给不同的服务配置不同的认证方式而无需更改服务程序，同时也便于向系统中添加新的认证手段。<br />​<br />
<a name="qYSDu"></a>
## PAM服务模块
PAM服务模块是一个共享库(动态链接库, 放在/lib/security下),用于为系统登录应用程序(如login、su、sssd等)提供验证和其它安全服务。对应的四种类型的PAM服务是：

1. 验证服务模块: 用于授予用户访问账户或服务的权限。提供此服务的模块可以验证用户并设置用户凭证；
1. 账户管理模块：用于确定当前用户的账户是否有效。提供此服务的模块可以检查口令或账户的失效期以及限时访问；
1. 会话管理模块：用于设置和终止登录会话；
1. 口令管理模块：用于强制实施口令强度规则并执行验证令牌更新。

​

一个PAM模块可以实现其中的一项或多项服务。将简单模块用于明确定义的任务中可以增加配置灵活性。因此，我们应该在不同的模块中实现PAM服务。然后安装对应的PAM配置文件中定义的方式根据需要使用这些服务。每个使用PAM认证的应用程序都是以`pam_start`开始，`pam_end`结束。实际做认证工作的API函数有6个(简称认证API）：

|  | 应用程序 API  | 服务模块 SPI  |
| --- | --- | --- |
| 认证管理 | pam_authenticate() | pam_sm_authenticate() |
| 认证管理 | pam_setcred() | pam_sm_setcred() |
| 账号管理 | pam_acct_mgmg() | pam_sm_acct_mgmg() |
| 会话管理 | pam_open_session() | pam_sm_open_session() |
| 会话管理 | pam_close_session() | pam_sm_close_session() |
| 密码管理 | pam_chauthok() | pam_sm_chauthok() |



<a name="feb1b0af"></a>
## PAM框架

<br />PAM框架主要由四部分组成：<br />

1. PAM应用程序，也称为消费方；
1. PAM库；
1. PAM配置文件；
1. PAM服务模块，也称为提供者；


<br />下图是对应的PAM体系结构。应用程序会通过对应的PAM应用编程接口 (application programming interface, API) 与 PAM 库进行通信。PAM 模块通过 PAM 服务提供者接口 (service provider interface, SPI) 与 PAM 库进行通信。通过这种方式，PAM 库可使应用程序和模块相互进行通信。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1202325/1642663856410-a4ca93f5-7b7f-4583-a3b9-3b8218b23264.png#clientId=u0dcbda83-cdc6-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=532&id=uc2b526e0&margin=%5Bobject%20Object%5D&name=image.png&originHeight=532&originWidth=689&originalType=binary&ratio=1&rotation=0&showTitle=false&size=22912&status=done&style=none&taskId=ue2cb3468-491e-4eaf-b33c-af937bce25d&title=&width=689)<br />该框架可为与验证相关的活动提供统一的执行方式。采用该方式，应用程序开发这可使用PAM服务，而不必了解整体策略的语义。借助PAM模块，管理员可以根据特定系统的需要调整验证过程，而不必更改任何应用程序。通过调整对应的PAM配置文件(/etc/pam.con，后面改为/etc/pam.d/下的对应配置文件)来执行。<br />

<a name="DXJv2"></a>
## PAM配置文件解析
在linux 系统中，PAM的配置文件在/etc/pam.d这个目录下，原来的/etc/pam.conf已基本不起到作用了，/etc/pam.d中对应的配置文件是以原来/etc/pam.conf中“service-name”的值命名的。如： sshd,login等，只是少了最左边的服务名列, 如：/etc/pam.d/sshd。<br />​

下面以欧拉服务器版中`sshd`服务的配置文件为例进行说明：
> $ cat sshd 
> #%PAM-1.0
> auth       substack     password-auth
> auth       include      postlogin
> account    required     pam_sepermit.so
> account    required     pam_nologin.so
> account    include      password-auth
> password   include      password-auth
> session    required     pam_selinux.so close
> session    required     pam_loginuid.so
> session    required     pam_selinux.so open env_params
> session    required     pam_namespace.so
> session    optional     pam_keyinit.so force revoke
> session    optional     pam_motd.so
> session    include      password-auth
> session    include      postlogin

由上面的pam模块文件内容可以看出，可以将pam配置文件分为四种类型：

- 第一列为模块类型；
- 第二列为控制标记；
- 第三列为模块路径；
- 第四列为模块参数；



<a name="q173B"></a>
### 1. PAM的模块类型
linux PAM模块有四种类型，分别代表四种不同的任务，分别是：** 认证管理（auth）, 账号管理（account）,会话管理（session）和密码（Password）, **一个类型可能有多行，按顺序依次由PAM模块调用。

| **管理方式** | **说明** |
| --- | --- |
| auth | 用来对用户的身份进行识别，如：提示用户输入密码或判断用户是否为root等 |
| account | 对账户的各项属性进行检查，如：是否允许登录，是否达到最大用户数或是root用户是否允许在这个终端登录等 |
| session | 这个模块是用来定义用户登录前的，及用户退出后所要进行的操作，如：登录连接信息，用户数据的打开与关闭，挂载文件系统等 |
| password | 使用用户信息来更新，如：修改用户密码 |

对以上四种模块类型进一步补充说明：

- auth:
- account:
- session:
- password:

​<br />
<a name="MgBsH"></a>
### 2. PAM的控制标记
<a name="RwnXL"></a>
### 3. 模块路径
<a name="WhONW"></a>
### 4. 模块参数
<a name="7e33bb5d"></a>
## PAM源码学习


<a name="d1f972b3"></a>
## 参考文档

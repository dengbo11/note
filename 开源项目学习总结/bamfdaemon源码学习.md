<a name="kFfse"></a>
## 整体介绍
`bamfdaemon`是基于`glib`, `gio`库，使用`c`语言来设计的一个开源项目，底层窗口处理使用的是x11的相关接口，它能将应用程序的窗口匹配到对应的桌面文件。
当打开一个应用程序时，Unity将在启动器（任务栏）上弹出一个新图标和/或向现有图标添加箭头。每个图标都不是可执行文件；它是一个.desktop文件。bamfdaemon通过确定是需要将箭头添加到现有图标还是弹出新图标，这取决于应用程序身份的行为，`bamfdaemon`项目可以帮助实现这一点。当前，UOS系统用户它来识别应用窗口与对应的任务栏图标(.desktop文件）。
​

<a name="TeDsf"></a>
## 源码目录介绍
.
├── data
├── debian
├── doc
├── lib
├── m4
├── src
└── tests
​


- data: `bamfdaemon`系统配置文件，在安装`bamfdaemon`时会将对应的服务和配置文件安装到系统中；
- debian: 存放对应打包规则文件，用于打包；
- doc: 文档目录，接口详细使用说明；
- lib: 代码使用的库文件；
- m4: 对应配置文件的生成文件；
- src: 源码目录；
- tests: 存放对应测试实例；
<a name="mZby5"></a>
## 代码学习
main.c: 主程序入口,获取对应选项参数，初始化`bamfdaemon`, 
bamf-application.c: 实现每个应用与窗口和桌面文件的对应；
bamf-control.c: 用于初始化`dbus`相应接口，并控制应用与桌面文件的注册和插入；
bamf-daemon.c: `bamfdaemon`的主逻辑实现；
bamf-legacy-screen.c: 处理窗口的打开和关闭，监听对应窗口变化，对对应窗口的显示进行排序；
bamf-legacy-window-test.c: bamf-legacy-screen.c中对应接口测试；
bamf-legacy-window.c: 获取应用的`pid`、窗口类型，窗口名字，窗口大小等信息，处理窗口状态的变化；
bamf-matcher.c: 窗口显示与应用桌面文件关联的主要实现，监听窗口视图的变化，并做对应处理。`dbus`关键接口的实现；
bamf-tab.c: 窗口对应`tab`（Ctrl+tab键）操作的显示逻辑处理；
bamf-view.c: 监听窗口属性改变，进行窗口视图处理。
bamf-window.c: 窗口对应接口实现，窗口属性处理。
bamf-xutils.c: 调用x的接口，实现对应窗口的接口处理。
​

<a name="NNQE9"></a>
## 代码逻辑框架
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1202325/1632146497652-10120a14-d80f-4185-9885-3e32e986589c.png#clientId=ufeb18183-5942-4&from=paste&height=192&id=u75b2fe69&margin=%5Bobject%20Object%5D&name=image.png&originHeight=383&originWidth=1121&originalType=binary&ratio=1&size=36044&status=done&style=none&taskId=u9190c00e-6085-47a8-a4f0-22459cdb259&width=560.5)
如上图所示，`BamfDaemon`对象是整个程序的主类，通过`BamfControl`和`BamfMatcher`对象对应用进行桌面文件与窗口的处理。其中，当每打开一个应用程序时，都会根据窗口的视图对象(BamfView), 生成一个应用对象(BamfApplication), 然后会通过窗口对象(BamfWindow)读取窗口的相关属性，将对应的应用和应用的桌面文件关联起来。以上，就是`bamfdaemon`识别应用窗口与桌面文件的大致流程。
<a name="GmSDC"></a>
## 调试及问题定位

1. 由于源码中使用glib的接口作为日志打印，所以我们通过常规手段(`journalctl`)是看不到日志输出的, 这将会给问题定位带来一定的难度。所以，目前定位问题的方式一般是源码调试以及监听对应的信号，然后结合分析源码去定位问题。
1. 源码编译：使用`apt source bamfdaemon`下载对应的源码，通过`sudo apt build-deb bamfdaemon`安装对应的依赖，然后执行`./autogen.sh`生成对应的配置选项，执行`make`进行编译。
1. 源码调试：配置好对应的调试环境(vscode)后，可通过打断点的方式调试`bamfdaemon`, 结合分析源码进行问题定位。
1. 监听对应`dbus`信号进行问题定位，一般通过`dbus-monitor --session interface=org.ayatana.bamf.matcher`监听对应的窗口改变信号。




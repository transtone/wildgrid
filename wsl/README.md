# wsl(Windows Subsystem for Linux) 简介
wsl 就是windows上的Linux子系统。在wsl之前，有两种方案可以在windows上跑linux：
1. cygwin，他通过一种方案，将 Linux(严格来说是GUN) 的软件编译成可以在windows下运行的exe文件。
2. 虚拟机，在虚拟机上安装Linux系统。
wsl 采用的是 cygwin 的思路；wsl2 则结合了上述两种思路，用了微软。

## wsl 解决的问题
1. Linux 内核运行与二进制文件的执行
2. 文件系统(权限、路径、磁盘)互通

## wsl 官方文档
* [microsoft wsl](https://docs.microsoft.com/zh-cn/windows/wsl/about)
* [wsl2](https://docs.microsoft.com/en-us/windows/wsl/wsl2-index)

## wsl 安装资源
* [官方发行版](https://docs.microsoft.com/en-us/windows/wsl/install-manual)
* [wsldl](https://github.com/yuk7/wsldl)

## wsl 中运行服务
wsl 中是可以运行服务的，但因为引导方式采用的是 scriptinit 方式，目前只对 ubuntu 的服务支持的比较好。而不采用 ScriptIint 方式，用 upstart，systemd 或 openrc 等启动程序的发行版，则支持不好。

* ubuntu 中运行服务，如ssh，直接 /etc/init.d/ssh start 就好。
* [wsl中自启动服务](https://zhuanlan.zhihu.com/p/47733615)

今天给wsl装上了gentoo，发现 openrc 用不了。爬了一天的网，可能是没有细看英文文档的缘故，没有找到一篇有价值的，可以解决 upstart/systemd/openrc 启动服务的文章。正当气馁之时，到 /etc/inittab 里看了看，发现首先运行的就是 /sbin/openrc，手动运行一下，再试试 /etc/init.d/sshd start，哈哈，跑起来了，一切如常！至此，wsl 终于可以当主力系统来用了，感谢微软，向wsl项目组的兄弟们致敬！

## wsl gentoo安装指南
1. 准备 Launch 程序。到 [wsldl官网](https://github.com/yuk7/wsldl/releases) 下载 icons.zip ，解压出里面的 `Gentoo.exe` 文件，放到一个单独的文件夹中。
2. 准备系统镜像。进入到 ubuntu 系统，下载最新的 stage3 包，用命令转换一下：`xz -d -c stage3-latest.tar.xz | gzip > rootfs.tar.gz` (进入 ubuntu 系统是为了借用其 xz 和 gzip 命令)。将转换后的 rootfs.tar.gz 文件放到 `Gentoo.exe` 的同一文件夹，双击 `Gentoo.exe`，安装完成。
3. 双击 `Gentoo.exe` 进入系统，装 portage、改配置、更新、安装工具软件。要运行系统服务，先 touch /run/openrc/softlevel，在运行 /sbin/openrc，即可正常启动系统服务了，用rc-update add 可以添加自动启动的服务哦。
4. 配置windows开机启动服务。创建一个 startwsl.vbs 的脚本，将其放入`启动`文件夹中(win-r 中输入 `shell: startup`打开)即可
```vbscript
Set ws = WScript.CreateObject("WScript.Shell")
ws.run "wsl -d gentoo -u root /etc/init.wsl", vbhide
```

## cmd/powershell 调整
windows 10 中的 cmd/powershell 已经有了长足的进步，窗口大小终于可以改了。可字体和复制粘贴还是那么难用。

1. 字体。必须用特定的字体，我习惯用的 firaCode 在进入 less/vim 之类的程序界面后，字体又变回去了。consolas 虽然可用，但 1 不支持 powerline，2 中文特丑，幸好有大神制作了[更纱黑体](https://github.com/be5invis/Sarasa-Gothic)，非常棒，解决了我的痛点。
2. 复制粘贴。首先作为windows的自带命令行，Ctrl-c/Ctrl-v 却用不了，还好可以用 Ctrl-Shift-c/Ctrl-Shift-v；其次左键拖选，右键粘贴的快捷键设置不太符合个人习惯。我没事就喜欢乱点左键右键活动手指，经常一不小心就复制了空白或粘贴了大段代码过来。还好有[ahk神器](https://github.com/Lexikos/AutoHotkey_L)，[改写了快捷键](https://gist.github.com/transtone/767b21daab393ebbeb84766ceda99a43)，舒服了。

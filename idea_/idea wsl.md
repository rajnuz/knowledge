# Idea 自动启动WSL虚拟机

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

本机安装过wsl虚拟机后，在Idea里进行一些操作，例如切换jdk，获取maven load会在系统进程中自动产生一个Vmmem进程，这个进程是因为Wsl虚拟机被Idea启动了，此时运行 `wsl -l --running` 会发现启动的wsl虚拟机。

如果虚拟机启动后有特别吃资源的服务，会将宿主机资源也占满，可以通过`wsl --shutdown`关闭

[彻底解决的办法](https://stackoverflow.com/questions/77213822/how-to-tell-intellij-not-to-use-wsl)

在Idea中点击Help -》 Find Action 然后输入Registry，选中Registry... 将里面所有wsl开头的选项都关掉，重启Idea
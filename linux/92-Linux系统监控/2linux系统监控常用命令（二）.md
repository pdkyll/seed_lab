---
show: step
version: 0.1
enable_checker: true
---

#linux系统监控常用命令（二）

## 一，实验简介

本节继续上一节实验，介绍 Linux 系统监控的其他常用命令。

### 1.服务管理程序：sysv-rc-conf

Linux 系统任何时候都运行在一个指定的运行级上，并且不同的运行级的程序和服务都不同，所要完成的工作和要达到的目的都不同，系统可以在这些运行级之间进行切换，以完成不同的工作。sysv-rc-conf是一个强大的服务管理程序，用于查看程序和服务的运行级别。

Ubuntu 的系统运行级别：
```
0　　 　系统停机状态

1　　 　单用户或系统维护状态

2~5　　多用户状态

6　　 　重新启动
```

安装并运行sysv-rc-conf：
```
$ sudo apt-get install sysv-rc-conf
$ sudo sysv-rc-conf
```

操作截图：

![2-1](https://doc.shiyanlou.com/userid42227labid973time1430792194009/wm)

操作界面十分简洁，你可以用鼠标点击，也可以用键盘方向键定位，用空格键选择在启动级中开启或关闭程序，用Ctrl+N翻下一页，用Ctrl+P翻上一页，用Q退出。

对程序进行启动级的操作也可以用update-rc.d命令实现。

例如：删除apache2随机器启动的服务,并查看其它命令选项
```
$ sudo update-rc.d -f apache2 remove
$ sudo update-rc.d 
```

### 2. 监视每个进程使用的网络带宽：NetHogs
NetHogs是一个开放源源代码的很小程序（与Linux下的top命令很相似），它密切监视着系统上每个进程的网络活动。同时还追踪着每个程序或者应用所使用的实时网络带宽。

```
# 安装 nethogs
$ sudo apt-get install nethogs

# 因为 0.8.0 的一个bug，需要先切换到 root 再执行，使用 sudo 会报错，安装 0.8.1 以上的版本就没问题了

# 实验环境中的 0.8.0 版本
$ sudo su
# nethogs

# 0.8.1 以上版本执行 sudo nethogs
$ sudo nethogs

```

操作截图：

![2-2-1](https://doc.shiyanlou.com/userid42227labid973time1430793208313/wm)

用Q退出nethogs的界面。

运行nethogs命令：
```
$ nethogs --help
# 实验环境中的 0.8.0 版本
$ sudo su
# nethogs > nethogs.txt
```
操作截图：

![2-2-2](https://doc.shiyanlou.com/userid42227labid973time1430793603418/wm)

打开主文件夹，查看nethogs.txt里面的内容。(由于实验环境对字符的支持问题，可能会出现乱码)。

### 3. 监视网络带宽：iftop
iftop是另一个在控制台运行的开放源代码系统监控应用，它显示了系统上通过网络接口的应用网络带宽使用（源主机或者目的主机）的列表，这个列表定期更新。iftop用于监视网络的使用情况，而'top'用于监视CPU的使用情况。iftop是'top'工具系列中的一员，它用于监视所选接口，并显示两个主机间当前网络带宽的使用情况。
```
$ sudo apt-get install iftop

$ sudo iftop
```
操作截图：

![2-3-1](https://doc.shiyanlou.com/userid42227labid973time1430794164800/wm)


界面相关说明:
```
<= => ：表示的是流量的方向。
TX：发送流量
RX：接收流量
TOTAL：总流量
Cumm：运行iftop到目前时间的总流量
peak：流量峰值
rates：分别表示过去 2s 10s 40s 的平均流量
```
界面操作：
```
按h切换是否显示帮助;

按n切换显示本机的IP或主机名;

按s切换是否显示本机的host信息;

按d切换是否显示远端目标主机的host信息;

按t切换显示格式为2行/1行/只显示发送流量/只显示接收流量;

更多操作请查看帮助
```

操作截图：

![2-3-2](https://doc.shiyanlou.com/userid42227labid973time1430794475596/wm)


### 4. System Monitor（图形化监视）
gnome-system-monitor是GNOME系统监视器，能够监听CPU，内存，进程，硬盘的信息。分为进程监控，资源监控，文件监控：
```
$ sudo apt-get install gnome-system-monitor

$ gnome-system-monitor
```

进程监控：包括进程名，用户，CPU占用率，进程PID，内存占用，优先级：

操作截图：

![2-4-1](https://doc.shiyanlou.com/userid42227labid973time1430811338557/wm)


资源监控是对历史资源使用的统计，将其绘制成图展现，从图中可以看出CPU使用率的变化，存储占用率变化，带宽占用率变化。

操作截图：

![2-4-2](https://doc.shiyanlou.com/userid42227labid973time1430811346901/wm)

----------


由于实验环境的原因，文件监控包括hosts，hostname等。

![2-4-3](https://doc.shiyanlou.com/userid42227labid973time1430811325871/wm)

----------


双击hosts，查看其内容：

操作截图：

![2-4-4](https://doc.shiyanlou.com/userid42227labid973time1430811858641/wm)

###参考文档：

1.http://www.bitscn.com/os/linux/201405/199059.html

## Linux常用命令

### 部署项目常用的命令

项目启动【后台不挂断运行jar包启动项目】

```shell
#Linux 运行jar包命令如下：
#方式一：
java -jar XXX.jar
#特点：当前ssh窗口被锁定，可按CTRL + C打断程序运行，或直接关闭窗口，程序退出

#那如何让窗口不锁定？
#方式二
java -jar XXX.jar &
#&代表在后台运行。

#特定：当前ssh窗口不被锁定，但是当窗口关闭时，程序中止运行。

#继续改进，如何让窗口关闭时，程序仍然运行？
#方式三
nohup java -jar XXX.jar &
nohup 意思是不挂断运行命令,当账户退出或终端关闭时,程序仍然运行
#当用 nohup 命令执行作业时，缺省情况下该作业的所有输出被重定向到nohup.out的文件中，除非另外指定了输出文件。

#方式四
nohup java -jar XXX.jar >temp.txt &
#解释下 >temp.txt
command >out.file
command >out.file
#是将command的输出重定向到out.file文件，即输出内容不打印到屏幕上，而是输出到out.file文件中
```

项目运行查杀进程【查看占用端口的进程并杀掉进程】

``` shell
#显示所有的进程
ps -ef

#查看那个端口被占用
netstat -tln
netstat -tln | grep 80

#终止进程运行
kill PID
#注意 PID是一串数字的进程号

```

系统服务管理命令【启动和停止mysql服务】

```shell
#输出系统中各个服务的状态：
systemctl list-units --type=service

#查看防火墙服务的运行状态：
systemctl status firewalld

#关闭防火墙服务
systemctl stop firewalld

#启动mysql服务
service start mysqld

#停止mysql服务
service stop mysqld

# 重启mysql服务
service restart mysqld
```

### 进程相关的命令

```shell 
# 查看所有用户不是在终端运行的进程详细信息
ps aux
# -a显示所有用户的所有进程 -x显示无终端的进程 -u显示更多信息，类似于 -f 选项 -e显示所有进程

#查看wz用户的所有进程
ps -ef | grep wz

# 查看所有运行java进程
ps -ef | grep java

# 查看后台正在跑的任务（终端工具断开就就会查不到）
jobs 
```

**jobs 和 ps命令查看进程的区别**：

ps是查看进程的命令
jobs是一个看后台正在跑的任务

jobs和ps是基于会话连接，即用xshell终端工具一次连接相当一个会话，不断开使用jobs和ps才能查到任务和进程

网上很多博客说 jobs配合nohup 查看后台进程；输入jobs命令,很可能啥也没有。。。这是为什么？ 答案就是：
jobs只能查看当前xshell连接服务器窗口创建的任务。由于在这个新连接下我并没有用nohup启动后台任务，所以就不会有显示

**top、jobs、ps命令的区别**

相对于ps和jobs而言，top命令工具类似于windows系统的任务管理器，可以动态的持续监控进程的运行状态

jobs是基于会话连接，ps显示的是某个时间点的进程状态信息，top不受时间限制

top -p 15411 或者ps aux命令 可以查到。 ps -ef | grep wz可以查看wz用户的所有进程（top无视会话连接，断了也能查到所有进程）

**小结**：查看后台运行的Java进程程序还是选择 `ps aux`或者`ps -ef | grep java`

### 文件管理命令

```shell
# 移动A目录下所有文件到B目录下
mv /A * /B
# mv重命名文件夹 如：将ngnix 命名为 nginx
mv ngnix nginx
# 删除命令 删除包含dist的所有东西 例如dist文件夹和dist.zip文件
rm -rf dist* 
# 目录创建
mkdir <directory>
# 创建文件 如创建nginx.conf
touch nginx.conf 
# 移动到文件夹命令 移动到usr目录下
cd usr/
# 查看输出当前所处的目录
pwd
# 查看java安装位置
whereis java
# 查看mysql安装位置
whereis mysql

```

参考文章：

1. [Docker常用命令](https://blog.csdn.net/zhang__jiayu/article/details/42611469)【主要讲镜像管理、容器管理、数据管理等命令】
2. [linux下执行并查看后台进程，jobs，ps，top命令 的区别](https://blog.csdn.net/qq_43778308/article/details/103553225)【如题，主要讲区别】
3. [Linux进程管理常用命令及监控工具](https://blog.csdn.net/lv8549510/article/details/80577701)【主要讲top命令和top命令增强工具】
4. [还在百度Linux命令？推荐一套我用起来特顺手的命令](https://juejin.cn/post/6877337031605911566)
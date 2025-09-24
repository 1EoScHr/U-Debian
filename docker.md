# Docker

~~之前总听说Docker有一系列好处，但一直没有用过。  
现在终于碰到了需要其上场的时候。~~  

省流：apt报出的卸载提示并不是两个工具链一定不能共存，而是新安装的这个x86 multilib占用了riscv的上游依赖，apt误以为riscv相关包是“孤儿包”，所以要自动清理。但是手动标记就不会。  

虽然相当于一天白干，但至少学了docker……  

# Why Docker

现在正在完成**操作系统**的课程，一方面在看MIT的网课，一边在学校OS课程的试点班。两课程都有实验，但是需要的环境不同：  
+ MIT的**xv6**基于RISC-V架构，需要用**qemu**的riscv
+ 试点班的miniOS是基于x86架构，要用到**qemu**的x86

我已经在本机上装好了riscv的相关组件并正常使用，在配置miniOS时却发现不能共存：  
```
下列软件包是自动安装的并且现在不需要了：
  cpp-12-riscv64-linux-gnu cpp-riscv64-linux-gnu gcc-12-cross-base-ports
  gcc-12-riscv64-linux-gnu-base libasan8-riscv64-cross
  libatomic1-riscv64-cross libc6-dev-riscv64-cross libc6-riscv64-cross
  libgcc-12-dev-riscv64-cross libgcc-s1-riscv64-cross libgomp1-riscv64-cross
  linux-libc-dev-riscv64-cross miniupnpc
使用'sudo apt autoremove'来卸载它(它们)。
 ……
 下列软件包将被【卸载】：
  gcc-12-riscv64-linux-gnu gcc-riscv64-linux-gnu
```

搜索一番，得知原因是**交叉工具链的包管理器冲突**：  
xv6依赖的*gcc-riscv64-linux-gnu* 和支持编译32位程序的*gcc-multilib/gcc-12-multilib*所依赖的*gcc-12*是一个交叉编译器的**不同变体**。  

如果不想做一边的作业就得卸载另一边的依赖，就只能考虑**隔离环境**。  
其中**虚拟机**开销大，不值得（之前上工训要用到一些win的工业软件，用VirtualBox强装了win10，结果感觉一坨）；或者用**chroot**在本机建一个独立的根文件系统，类似轻量级虚拟机，也能隔离包依赖（但这种操作一点也不熟悉，太陌生了）；再或者就是用**Docker**了。  

**Docker的好处**：  
+ **磁盘占用小**，一个完整的虚拟机桌面大约在10GB，而Docker拉取来的ubuntu镜像只有300MB左右，用更纯净的Debian甚至只有100MB左右
+ **原因**：Docker不需要模拟硬件与内核，其只是一个**user态程序**，内核调用直接走宿主机的**linux内核**，因此我的debian机器可以混着跑ubuntu、fefora等其他Linux发行版；也因此不能够跑windows与mac（也能用QEMU这种虚拟化，但这时候开销就类似虚拟机了）
+ ***如果没有学操作系统，看上面的这些所谓“用户态”、“内核”是肯定不太能理解的；但现在虽然只学了个皮毛，但至少能答题理解这Docker的妙处***  

**使用Docker的本质就是配置一个特定的环境，运行宿主机不能干的事情。**  

# How Docker

跟着GPT搞。  

## 安装Docker

```
sudo apt update
sudo apt install -y docker.io # 早期有一个不相干的程序占用了docker
sudo systemctl enable --now docker
sudo usermod -aG docker $USER   # 把自己加入 docker 组，重登生效
```

由于这学期要一直写OS作业，所以就把docker服务添加到自启动。  

### 意外收获：开机启动管理

在研究上面的这句`sudo systemctl enable --now docker`时，发现似乎经常在安装某些东西时会开启这个服务开机自启动，询问了一下，尽管大部分都是系统必用或十分轻量级的功能，但还是有些可能是不会用到的重量级服务，会有明显的性能占用。  

于是使用`systemctl list-unit-files --type=service`来查看当前所有服务的开机启动情况，把结果交给GPT分析，果然有几个是一般情况下不怎么会用的：  
+ **mysql.service**：前几天刚为了数据库实验安装的数据库服务器，其占用开销挺大，但我只有上实验课的时候才会用。所以可以**考虑禁用**，要用到时手动`sudo systemctl start mysql`开启服务就好。
+ **vbox\*.service**：这就是之前装虚拟机的相关服务，现在已经基本不用，也禁用好了。
+ **zerotier-one.service**：为了上鸿蒙应用开发时远程连接宿舍里的windows装的内网穿透，但最后没能成功，放弃，删了吧  

关闭方法就是`sudo systemctl disable --now xxxx.service`，会立刻停止并取消开机自启动；当想要恢复开机自启动就把`disable`改成`enable`即可；需要手动开启，用`sudo systemctl start xxxx.service`。  

开机各服务消耗时间也可以用`systemd-analyze blame`来获取，从而分析哪个不用的服务时间太长。  

## 配置docker

安装好docker后就要pull来一个系统，我自然选择拉取debian镜像：`docker pull debian:stable`。  

但显然这种要从云端下载的东西都不可能直接下载到，报了错。于是很熟练的在网上查找dock换源方法，大概内容是：  
+ 创建`/etc/docker/daemon.json`路径，并进入编辑，加上镜像站的json，具体内容直接找网上的；另外这里面还不能有"//"这样的注释
+ 执行`sudo systemctl daemon-reexec`，重新解析地址
+ 执行`sudo systemctl restart docker`重启服务即可。

这样就能够再次pull到所要的镜像。  

## 创建/保存/挂载docker

在这之前，需要明确一点：**镜像本身是静态的，容器只是上面的可写层**。  

用`docker run -it --name NPUos_x86_deb debian:stable bash`在得到的debian镜像上创建一个有名docker并进入，可以看到是极其的简陋（只包括最基本的C库和工具，剩下啥都没有，还是英文界面）  

与新安装的debian完全一样，还得换apt源等。换的时候发现甚至nano/vim都没有，安装后换源，下载时报错，似乎是无法访问，原因是没有安装CA证书，继续安装，终于是能正常的apt安装东西了。  其他东西就略过。  

docker相当于是一个固定的特制环境，在里面干的东西并不会自动保存，我们这样只是进入了一个**容器实例**，不保存的话，退出后就会消失。  
现在在这个有名实例里配置好了做作业所需要的环境，可以commit将这个实例变为一个新镜像，这样就能直接用这个环境了：`docker commit NPUos_x86_deb os_x86_deb`（注意这里，新的镜像名不能有大写；那么为啥前面那个有呢？因为其是最开始那个debian:stable镜像的实例）  

这样在完成作业后，需要编译时，就在这个新的镜像上挂载仓库目录，这样会创建一个临时容器，在上面执行，并把得到的文件放回去，命令为`docker run -it -v ~/Documents/NPUOS_Assignment:/home/os os_x86_deb:latest bash`，这样会在这个完整够用的image上建立一个匿名临时docker（因为就纯用这个环境了，不需要再对环境本身做别的）并把指定仓库挂载到/home/os上，这样编译东西就够了。  

**这样基本就算是docker入门了，足够继续作业，也放到这里供以后需要用docker的时候复习**。  

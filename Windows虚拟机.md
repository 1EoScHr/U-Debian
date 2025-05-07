# windows虚拟机

由于课程需要，不得不安装windows上的软件；可是不会考虑双系统（给U盘上装倒也在考虑范围内），先在**VirtualBox**上安装**win10虚拟机**。  
其作为mylinux的一部分，因此记录在此。  

## VirtualBox安装

+ 先到[官网处](https://www.virtualbox.org/wiki/Linux_Downloads)下载对应的deb包
+ 然后`dpkg -i xxx`进行解包安装
+ 在安装时提醒缺少“libxcb-cursor0”库，手动安装即可：` sudo apt install libxcb-cursor0` 

## win10镜像安装

有**两种方法**可选：  
+ [微软官网](https://www.microsoft.com/zh-cn/software-download/windows10ISO/)下载，速度快，但可能需要验证，导致连接失败无法安装
+ 还有就是网上的第三方提供的镜像源，速度比较慢，有一个口碑较好的：[MSDN](https://msdn.itellyou.cn/)，但提供的是ED2K与BT链接，速度感人，或许有解决方案？  

### 补充：Gopeed下载器

没怎么用过别的，这个开源且评价比较高，在win、linux都能用，还是可以的。  
+ github：[Gopeed](https://github.com/GopeedLab/gopeed)下载release的deb包，  
+ `dpkg -i xxx`安装
+ 同样提示缺少包gir1.2-ayatanaappindicator3-0.1，手动安装`sudo apt install gir1.2-ayatanaappindicator3-0.1`再来一次即可。  

## 配置与打开虚拟机

1. **常规**的，新建虚拟机，选择镜像、机器配置，根据性能合理安排，其余默认

2. 配置完成后，打开**报错**："Kernel driver not installed (rc=-1908)"，大致是linux内核中和虚拟机相关的**模块驱动**没有安装。  
  根据报错提示，在命令行用`sudo /sbin/vboxconfig`来**编译安装驱动**；  
  但是还有可能提示缺少虚拟机需要的**内核头文件**，则更新依赖源后，用`sudo apt install linux-headers-$(uname -r)`来安装，其中，`uname -r`用来获取当前内核版本  

  ***这东西和显卡驱动差不多，每次内核更新后都需要重新安装！！！***

3. 继续重新打开，虚拟机却一闪而过，查看报错信息，原因是bios中的内存**虚拟化**等保护技术没有开启，无法进行虚拟机服务。  
  可以通过`lscpu | grep Virtualization`来查看当前相关状态，没有输出就意味着没有开启。  
  重启，在bios中的**虚拟化Virtualization**设置中开启VT-x、VT-d等（intel的U）即可。  

4. 现在可以进去了，但是在准备安装的界面，会提示“Windows无法从无人参与应答文件读取\<ProductKey\>设置”，原因是VB会默认加载**软驱设备**，需要在虚拟机设置的**“存储”**部分直接卸载**floppy软驱**，保存后再次启动。  
  如果还是相同报错，则删除，再建立一个新的虚拟机，抢在刚建立好没有进系统时搞定上述步骤。  

## 安装、密钥与使用

剩下的内容暂未遇到问题。  
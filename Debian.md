# 我的linux配置：U-debian & x1carbon

## 前言

由于下述原因，[之前的配置文件](Debian_Old.mkd)干脆也放弃，重新开始，在这个文件里面进行配置描述。也弥补了我觉得之前写的太乱，没有条理。  

> 寒假回去x1c内存坏了，胡乱操作导致U-deb也被趁乱干掉。开学后为了配置GAMES101等网课的环境，苦于x1c还没有修好，直接重新安装U-deb。 2025.3.1  
> x1c终归是修好。只是那些寒假没来得及同步到github的笔记都寄了。那就坦然重新开始。2025.3.8  

***

## part1：linux安装

我选择的是**Debian-Gnome**，十分优雅，尽管它是最简单的默认项，但本人的确很喜欢。  

### U-debian安装：Kingston DataTraveler Max 256GB

1. **虚拟机配置**  
牢记[这篇指南]("")中的**三个要点**：usb3.0、UEFI、物理磁盘（管理员打开）。  
2. **配置apt阶段**  
在配置apt时，尽管选择国内镜像，但安全源仍为官网，连接极慢，是平时安装最耗费时间的。因此在后半段要**及时ESC**，爆红就说明没问题了。  
3. **修改EFI**  
根据指南修改时，要将ISO的**EFI中boot文件夹**复制到U-deb中，**不是单个的那个boot文件夹！！！**。  

#### x1carbon安装：Lenovo ThinkPad X1 Carbon 2018

实体机的安装感觉十分顺畅，相比于在u盘里配置。安装过程中是否还要趁机取消安全源配置我也忘了，甚至是否用图形界面安装也忘了，应该是要的吧……  

实体机重要的是分区，之前有/boot空间不足，都放不下两个版本的内核导致寄，所以还是要慎重。  
这台目前是256GB，分区情况：  
+ **256MB**--->**/boot/efi**  
+ **768MB**--->**/boot**  
+ **32GB**--->**/**  
+ **207GB**--->**/home**  
+ **16GB**--->**swap**  

以后换硬盘、换机子都可以按照这个比例。  

***

## part2：系统配置

***刚装好系统必做。***  

1. **sudo权限**  
	先用nano编辑罢，忍忍～  
	
2. **换源**  
	根据清华源即可，略  
	
3. **驱动**   
	***x1c不必装nvidia驱动***  

	```bash
	sudo apt install firmware-iwlwifi  
	sudo apt install nvidia-driver  
	sudo reboot  
	sudo apt install firmware-linux-nonfree  
	sudo apt install isenkram && isenkram-autoinstall-firmware  
	sudo apt install firmware-linux  
	```
	
4. **home去中文文件夹**  

	```bash
	cd ~  
	mkdir Desktop Download Templates Public Documents Music Pictures Videos  
	rm -rf 公共 模板 视频 图片 文档 下载 音乐 桌面  
	xdg-user-dirs-update --set DESKTOP ~/Desktop  
	xdg-user-dirs-update --set DOWNLOAD ~/Download  
	xdg-user-dirs-update --set TEMPLATES ~/Templates  
	xdg-user-dirs-update --set PUBLICSHARE ~/Public  
	xdg-user-dirs-update --set DOCUMENTS ~/Documents  
	xdg-user-dirs-update --set MUSIC ~/Music  
	xdg-user-dirs-update --set PICTURES ~/Pictures  
	xdg-user-dirs-update --set VIDEOS ~/Videos  
	```

5. **黑暗模式**  
	略  
	
6. **字体**  
	先到自己star的字体那里下载好。  
	```bash
	cd Download/  
	cd Consolas-Nerd-LXGW-Wenkai-Mono-main/  
	sudo mkdir /usr/share/fonts/myFonts  
	sudo cp consolaslxgw.ttf /usr/share/fonts/myFonts/  
	sudo fc-cache -f -v  
	fc-list | grep "consolaslxgw.ttf"  
	sudo reboot  
	```
	
7. **工具**
	安装基本的一些东西
	
	```bash
	sudo apt install vim build-essential gdb cmake
	```
	
	
***

## part3：基本软件配置

1. **输入法**  

	```bash
	sudo apt remove ibus  
	sudo apt install fcitx5-skin-nord  	
	```
	"优化"中设置开机自启+输入法字体配置  

2. **git**  

	```bash
	sudo apt install git  
	git config --global user.name "xxx"  
	git config --global user.email "xxx"  
	ssh-keygen -t rsa -b 4096 -C "xxx"  
	cat .ssh/id_rsa.pub  
	```

3. **IDE**  
	自行下载VSC，字体更换方式：  
	快捷键`ctrl+,`，然后在字体那里输入`'consolaslxgw', monospace`  
	
4. **MarkDown**  

	~~sudo apt install ghostwriter~~
	
	在x1c上ghostwriter有点问题，闪退严重，于是尝试**Typora**，的确挺漂亮，但是要收费，还好可以破解，先欠着，以后支持回来:(，教程为[这篇指南](https://blog.csdn.net/xueyou0910/article/details/145868822)  
	
5. **科学上网**  
	cutecloud+hiddfy  
	
***

## 进阶配置

+ **neofetch**  
系统信息获取  
+ **nnn**  
很好用、直观的文件查看  
+ **tmux**  
很牛逼的终端复用工具，见[tmux指南](tmux.md)  

***

## 使用技巧

### 在**u-deb**与**x1c**之间传递大文件：  
	做作业时为了在两台机器之间传递数据集，查到了几种方式。各自的速度、安全性等都不同。  
	但是在传递数据集这一场景下，速度最重要，所以使用**netcat**方式：  
	+ 在发送端：  
	`cat largefile.tar.gz | nc -l -p 12345`  
	+ 在接收端：  
	`nc 192.168.1.2 12345 > largefile.tar.gz`

> 查看本机ip方式：用`ip addr show`命令，在输出端寻找“inet 192.168.1.119/24 brd 192.168.1.255 scope global dynamic noprefixroute wlp2s0”，其中**192.168.1.119**就为本机ip。  

此方法是最快的，但数据没有加密，相当于“裸传”，但是对这个需求是最有效的。  

### .gitignore

在写python实现机器学习时，往往要实现代码与数据的分离，这个可以放在不同的文件夹来实现；  
但是习惯性的`git add .`会导致将数据集、模型文件这类大文件传入，否则手动一个一个添加十分麻烦。  

好在git提供有自定义忽略的功能，创建一个**.gitignore**，就能在其中指定不需要的文件。  
以一个机器学习demo为例，不需要**__pycache__**文件夹内的缓存、生成的.plt模型参数文件、Running文件夹中的数据集，所以可以在**.gitignore**中添加如下：  
```
# cache
__pycache__/
*.pyc

# model file
*.pth

# dataset
finalWork/Running/
```
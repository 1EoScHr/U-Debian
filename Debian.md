# 新版U-debian配置

自从寒假回去x1c崩掉，以为是u-deb的问题，将其整崩掉了。近来为了配置GAMES101的环境，苦于x1c还没有修好，直接重新安装，全新安装。 2025.3.1  

## part1：安装要点
1. **虚拟机配置**  
牢记[这篇指南]("")中的**三个要点**：usb3.0、UEFI、物理磁盘（管理员打开）。  
2. **配置apt阶段**  
在配置apt时，尽管选择国内镜像，但安全源仍为官网，连接极慢，是平时安装最耗费时间的。因此在后半段要**及时ESC**，爆红就说明没问题了。  
3. **修改EFI**  
根据指南修改时，要将ISO的**EFI中boot文件夹**复制到U-deb中，**而不是单个的那个boot**。  

## part2：系统配置
1. **sudo权限**  
	略  
	
2. **换源**  
	根据清华源即可，略  
	
3. **闭源驱动**  

	sudo apt install firmware-iwlwifi  
	sudo apt install nvidia-driver  
	sudo reboot  
	
	sudo apt install firmware-linux-nonfree  
	sudo apt install isenkram && isenkram-autoinstall-firmware  
	sudo apt install firmware-linux  

4. **home去中文文件夹**  

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

5. **黑暗模式**  
	略  
	
6. **字体**  

	cd Download/  
	cd Consolas-Nerd-LXGW-Wenkai-Mono-main/  
	sudo mkdir /usr/share/fonts/myFonts  
	sudo cp consolaslxgw.ttf /usr/share/fonts/myFonts/  
	sudo fc-cache -f -v  
	fc-list | grep "consolaslxgw.ttf"  
	sudo reboot  

## part3：基本软件配置
1. **输入法**  

	sudo apt remove ibus  
	sudo apt install fcitx5-skin-nord  
	优化中开机自启  

2. **git**  

	sudo apt install git  
	git config --global user.name "xxx"  

 	git config --global user.email "xxx"  
 	ssh-keygen -t rsa -b 4096 -C "xxx"  
 	cat .ssh/id_rsa.pub  

3. **IDE**  
	自行下载VSC，字体更换方式：  
	ctrl+, -> 'consolaslxgw', monospace  
	
4. **MarkDown**  

	~~sudo apt install ghostwriter~~
	
	在x1c上ghostwriter有点问题，闪退严重，于是尝试**Typora**，的确挺漂亮，但是要收费，还好可以破解，先欠着，以后支持回来:(，教程为[这篇指南](https://blog.csdn.net/xueyou0910/article/details/145868822)  
	
5. **科学上网**  
	cutecloud+hiddfy  
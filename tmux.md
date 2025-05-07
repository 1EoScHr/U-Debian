# tmux

刷b站刷到[这个视频](https://www.bilibili.com/video/BV18GXsY4Eze/?spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=3ae5bbb47e51877952f949c5dfa350dc)，讲解了**tmux**的快速上手、程序层次、多种模式，这让我有点想起来missing semester的vim课程，也是一个复杂又逻辑清晰的高效工具，挺有意思的。  
正好想换个高效终端，这个对于ssh连接很有用，有助于折腾那些板子。  
***Let's go.***  

***

# 上手与层次

`sudo apt install tmux`  
tmux有一组**重要按键**：ctrl+b，是tmux的**前缀键Prefix Key**，按下后相当于进入了tmux的待命状态，等待着后缀按键的到来以实现功能。（事实上，这些按键都可以自定义更改，在tmux配置问文件）  
tmux由**三个层次**组成：**session**、**window**、**panes**，依次包含。  
tmux还有非常强大的复制模式。  

## session Layer 会话层

一个会话就是终端里运行的另一个终端  

1. **新建session**
	+ `tmux`、`tmux new`  
	会直接新建一个默认会话，  
	+ `tmux new -s sessionname`  
	同样会创建一个新会话，但因为-s参数，所以有指定的名称，而不是默认分配的数字序号，s是session  
2. **分离session**
	+ **前缀+d**  
	会退出对话(detached from session)，但其并没有停止，会话还在后台运行  
3. **连接session**
	+ `tmux a`  
	会返回上一个分离的会话，这里a就是attach  
	+ `tmux a -t sessionname`  
	用-t参数指定要返回的会话名称（或者叫索引），t为target  
3. **终止session**
	+ `tmux kill-session`  
	终止最近的一个对话  
	+ `tmux kill-session -t sessionname`  
	终止指定的对话  
	+ `tmux kill-server`  
	终止所有对话  
4. **列出所有session**
	+ `tmux ls`  
	像列出文件一样列出所有会话  

## window Layer 窗口层

在创建一个会话时，会默认为我们在会话里创建一个窗口  

1. **创建window**  
	+ 当默认或指定创建session时，都会创建一个默认window  
	+ `tmux new -t windowname`  
	会自动创建一个默认会话，并在其中创建一个指定名称的window  
	+ **前缀+c**  
	新建并切换到newwindow  
2. **重命名window**
	+ **前缀+,**  
	重命名当前window  
3. **切换window**
	+ **前缀+n**  
	会按顺序切换window  
	+ **前缀+w**（***为什么不早说！***）  
	极其详细的、**直接在不同session、window、pane可视化切换**  
4. **关闭window**
	+ **前缀+&**  
	关闭当前window  

## panes Layer 分屏窗格

可以把窗口终端分成不同小部分  

1. **新建pane（分割window）** 
	+ **前缀+%**  
	纵向分割当前选中的终端(hotdog style)  
	+ **前缀+"**  
	水平分割当前选中的终端(hanburger style)  
2. 切换
	+ **前缀+方向键**
	指定方向切换panes  
	+ **前缀+q  对应序号**  
	按下后会显示panes序号，再按下对应序号就可切换  
3. 调整大小
	+ **前缀+ctrl or alt+方向键**
	实时调节当前panes大小  
	+ **前缀+alt+1to5**  
	根据默认预设自动调整panes  
4. 删除pane
	+ **前缀+x**  
	删除当前pane  

## 复制模式

### 首先更改配置文件

新建"~/.tmux.conf"配置文件，增加以下内容：  

```
set -g mouse on  
set -g mode-keys vi  
```  

### 在tmux终端中复制

#### 鼠标复制

用鼠标左键拖选即可复制  

#### 键盘复制

+ **前缀+{**
进入复制模式，通过vim类似的逻辑选择复制对象，按**space**开始，选择完毕后按**enter**完成复制  

#### 粘贴

应该与系统不共享  
+ **前缀+}**  
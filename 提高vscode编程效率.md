# VSCode 工具

## 一. 原生使用方法

### Show All Commands 

Shift+Command+P

### Selection菜单

#### 扩展/收缩选择区

Shift+Control+left / right

#### Move Line Down/Up

Meta + down/up

#### 按word移动

Meta+left/right

### View菜单

#### Zen Mode

跟F11类似

#### SideBar

显示文件目录，占用空间，一般不需要。直接用command+p进行搜索

#### Zoom In/Out

放大或缩小

command+ =/-

#### Split Down/Up

#### Toggle word wrap

如果触发了，大于80列的长度，会wrap，会在下一行显示，没有拖动条

### Go菜单

代码导航，很重要

#### Go to symbol in file

#### Go to definition/Type definition/ Implementation

这三个类似, 掌握一个就行

#### Go to file

Command+p

#### Back

Control+]

#### Last Edit Location

#### Go to Bracket

快速定位到符号的匹配项

结合后面的vim插件，可以实现选中对应括号里的内容

## 二.使用vim插件

为什么使用vim插件

因为vscode要适配windows快捷键，所以导致使用原生的快捷键，很麻烦，不太实用。

### 代码导航相关全局快捷键优化

#### Go to definition

Command+] 

#### Back

Command+t

### vscode配置设置

Open Settings(JSON)

https://github.com/redguardtoo/vscode-setup/blob/master/settings.json

将上面的链接替换掉之前的setting.json内容

### File菜单优化

#### Open recent

快捷键：,rr

#### Open File

快捷键：,xf ===== C-x C-f

#### Save File

快捷键：,xs

togglePanel

快捷键：F12 

### Edit菜单优化

#### Undo 

快捷键：u

#### Paste

快捷键：p

#### Copy 

快捷键：yy

#### 注释  

快捷键:  ,ci

#### findInFiles 

快捷键: ,qq

### Selection菜单优化

#### Expand Selection

快捷键：,xx

#### Shrink Selection

快捷键：,zz

#### 列模式 Visual Block

快捷键：C-v

再按大写I，进入编辑模式

#### 单行选中 Visual Line

快捷键：V

#### Text Obejct

vw (visual word)

vit（visual inside tag)

vat

vi{

Va{

vi`

cs`"(c代表change，s 代表surround)

### View菜单优化

#### Zen Mode

,ff

#### LayoutSingle

,x1

#### SplitRight

,x3

#### splitDown

,x2

### Go菜单优化

#### Back

C-]

#### Go to Type Definition

C-T

#### Last Edit Location

C-o 

#### 跳转到更新的位置

C-i

:jumps 

查看跳转表

#### Goto Symbol

,ii

#### Goto File

,kk

#### Go to Bracket

可以用vv，然后（,ss），然后就可以替换

### 代码自动补全

C-x C-l 自动完成行

跳到declaration声明： gd


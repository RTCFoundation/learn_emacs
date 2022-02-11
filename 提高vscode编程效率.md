# VSCode 工具

## 一. 原生使用方法

### Show All Commands 

Shift+Command+P

### Selection菜单

| 功能           | 快捷键              |
| -------------- | ------------------- |
| 扩展选择区     | Shift+Control+left  |
| 收缩选择区     | Shift+Control+right |
| Move Line Down | Meta + down         |
| Move Line Up   | Meta + up           |
| 按word移动     | Meta+left/right     |

### View菜单

| 功能             | 解释或快捷键                                                 |
| ---------------- | ------------------------------------------------------------ |
| Zen Mode         |                                                              |
| SideBar          | 显示文件目录，占用空间，一般不需要。直接用command+p进行搜索  |
| Zoom In          | command+ -                                                   |
| Zoom Out         | command+ =                                                   |
| Split Down       |                                                              |
| Split Up         |                                                              |
| Toggle word wrap | 如果触发了，大于80列的长度，会wrap，会在下一行显示，没有拖动条 |

### Go菜单

代码导航，很重要

| 功能                                             | 快捷键                   |
| ------------------------------------------------ | ------------------------ |
| Go to symbol in file                             |                          |
| Go to definition/Type definition/ Implementation | 这三个类似, 掌握一个就行 |
| Go to file                                       | Command+p                |
| Back                                             | C-]                      |
| Last Edit Location                               |                          |
| Go to Bracket                                    | 快速定位到符号的匹配项   |



## 二.使用vim插件

为什么使用vim插件

因为vscode要适配windows快捷键，所以导致使用原生的快捷键，很麻烦，不太实用。

### 代码导航相关全局快捷键优化

### vscode配置设置

Open Settings(JSON)

https://github.com/redguardtoo/vscode-setup/blob/master/settings.json

将上面的链接替换掉之前的setting.json内容

### File菜单优化

| 功能        | 快捷键 |
| ----------- | ------ |
| Open recent | ,rr    |
| Open File   | ,xf    |
| Save File   | ,XS    |
| togglePanel | F12    |

### Edit菜单优化

| 功能          | 快捷键 |
| ------------- | ------ |
| Undo          | u      |
| comment       | ,ci    |
| Find in files | ,qq    |

### Selection菜单优化

| 功能                 | 快捷键                              |
| -------------------- | ----------------------------------- |
| Expand Selection     | ,xx                                 |
| Shrink Selection     | ,zz                                 |
| Visual Block(列模式) | C-v,然后再按I，就可以进入到编辑模式 |
| Visual Line(行模式)  | V                                   |



#### Text Obejct

| 快捷键                            |
| --------------------------------- |
| vw(visual word)                   |
| vit(visual inside tag)            |
| vat                               |
| vi{                               |
| va{                               |
| vi`                               |
| cs`"(c代表change，s 代表surround) |

### View菜单优化

| 功能         | 快捷键 |
| ------------ | ------ |
| Zen Mode     | ,ff    |
| LayoutSingle | ,x1    |
| SplitRight   | ,x3    |
| splitDown    | ,x2    |

### Go菜单优化

| 功能                  | 快捷键 |
| --------------------- | ------ |
| Back                  | C-]    |
| Go to Type Definition | C-t    |
| Last Edit Location    | C-o    |
| 跳转到更新的位置      | C-i    |
| 查看跳转表            | :jumps |
| Goto Symbol           | ,kk    |
| Go to Bracket         | %      |

高级应用

用vv，然后（,ss），然后就可以替换

### 代码自动补全

C-x C-l 自动完成行

跳到declaration声明： gd


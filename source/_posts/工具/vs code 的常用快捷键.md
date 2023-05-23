---
title: vs code 的常用快捷键
cover: false
top: false
toc: false
mathjax: false
tags:
  - vs code
categories:
  - 工具
abbrlink: 23375
date: 2019-01-21 07:18:43
author:
img:
coverImg:
password:
summary:
keywords:
---
# 一、vs code 的常用快捷键
1、注释：

　　a) 单行注释：[ctrl+k,ctrl+c] 或 ctrl+/

　　b) 取消单行注释：[ctrl+k,ctrl+u] (按下ctrl不放，再按k + u)

　　c) 多行注释：[alt+shift+A]

　　d) 多行注释：/**

2、移动行：alt+up/down

3、显示/隐藏左侧目录栏 ctrl + b

4、复制当前行：shift + alt +up/down

5、删除当前行：shift + ctrl + k

6、控制台终端显示与隐藏：ctrl + ~

7、查找文件/安装vs code 插件地址：ctrl + p

8、代码格式化：shift + alt +f

9、新建一个窗口 : ctrl + shift + n

10、行增加缩进: ctrl + [

11、行减少缩进: ctrl + ]

12、裁剪尾随空格(去掉一行的末尾那些没用的空格) : ctrl + shift + x

13、字体放大/缩小: ctrl + ( + 或 - )

14、拆分编辑器 : ctrl + 1/2/3

15、切换窗口 : ctrl + shift + left/right

16、关闭编辑器窗口 : ctrl + w

17、关闭所有窗口 : ctrl + k + w

18、切换全屏 : F11

19、自动换行 : alt + z

20、显示git : ctrl + shift + g

21、全局查找文件：ctrl + shift + f

22、显示相关插件的命令(如：git log)：ctrl + shift + p

23、选中文字：shift + left / right / up / down

24、折叠代码： ctrl + k + 0-9 (0是完全折叠)

25、展开代码： ctrl + k + j (完全展开代码)

26、删除行 ： ctrl + shift + k

27、快速切换主题：ctrl + k / ctrl + t

28、快速回到顶部 ： ctrl + home

29、快速回到底部 : ctrl + end

30、格式化选定代码 ：ctrl + k / ctrl +f

31、选中代码 ： shift + 鼠标左键

32、多行同时添加内容（光标） ：ctrl + alt + up/down

33、全局替换：ctrl + shift + h

34、当前文件替换：ctrl + h

35、打开最近打开的文件：ctrl + r

36、打开新的命令窗：ctrl + shift + c



### 主命令框

> `F1` 或 `Ctrl+Shift+P`: 打开命令面板。在打开的输入框内，可以输入任何命令
>
> - 按一下 `Backspace`会进入到 `Ctrl+P`模式
>   在
> - `Ctrl+P` 下输入 > 可以进入 `Ctrl+Shift+P` 模式

**在 `Ctrl+P` 窗口下还可以**

- 直接输入文件名，跳转到文件
- `?` 列出当前可执行的动作
- ! 显示 `Errors`或 `Warnings`，也可以 `Ctrl+Shift+M`
- : 跳转到行数，也可以 `Ctrl+G` 直接进入
- @ 跳转到 `symbol`（搜索变量或者函数），也可以 `Ctrl+Shift+O`直接进入
- @ 根据分类跳转 - `symbol`，查找属性或函数，也可以 `Ctrl+Shift+O`后输入:进入
- \#根据名字查找 `symbol`，也可以 `Ctrl+T`

### 编辑器与窗口管理

- 打开一个新窗口： `Ctrl+Shift+N`
- 关闭窗口： `Ctrl+Shift+W`
- 新建文件 `Ctrl+N`
- 文件之间切换 `Ctrl+Tab`

### 代码编辑

#### 格式调整

- 代码行缩进 `Ctrl+[` 、 `Ctrl+]`
- `Ctrl+C`、 `Ctrl+V` 复制或剪切当前行/当前选中内容
- 代码格式化： `Ctrl+Shift+P` 后输入 `format code`
- 上下移动一行：`Alt+Up`或 `Alt+Down`
- 向上向下复制一行： `Shift+Alt+Up`或 `Shift+Alt+Down`
- 在当前行下边插入一行 `Ctrl+Enter`
- 在当前行上方插入一行 `Ctrl+Shift+Enter`

#### 光标相关

- 移动到行首： `Home`
- 移动到行尾： `End`
- 移动到文件结尾： `Ctrl+End`
- 移动到文件开头： `Ctrl+Home`
- 移动到定义处： `F12`
- 多行编辑(列编辑)：`Alt+Shift+`鼠标左键
- 同时选中所有匹配： `Ctrl+Shift+L`

### 重构代码

- 找到所有的引用： `Shift+F12`
- 同时修改本文件中所有匹配的： `Ctrl+F12`
- 重命名：比如要修改一个方法名，可以选中后按 `F2`，输入新的名字，回车，会发现所有的文件都修改了
- 跳转到下一个 `Error` 或 `Warning`：当有多个错误时可以按 `F8` 逐个跳转

### 查找替换

- 查找 `Ctrl+F`
- 查找替换 `Ctrl+H`
- 整个文件夹中查找 `Ctrl+Shift+F`

### 显示相关

- 全屏：`F11`
- 侧边栏显/隐：`Ctrl+B`
- 显示资源管理器 `Ctrl+Shift+E`
- 显示搜索 `Ctrl+Shift+F`
- 显示 `Debug Ctrl+Shift+D`

# 二、vs code 的常用插件
1、Auto Rename Tag 修改html标签，自动帮你完成尾部闭合标签的同步修改，和webstorm一样。

2、Auto Close Tag 自动闭合HTML标签

4、Beautiful 格式化代码的工具

5、Dash Dash是MacOS的API文档浏览器和代码段管理器

6、Ejs Snippets ejs 代码提示

7、ESLint 检查javascript语法错误与提示

8、File Navigator 快速查找文件

9、Git History(git log) 查看git log

10、Gulp Snippets 写gulp时用到，gulp语法提示。

11、HTML CSS Support 在HTML标签上写class智能提示当前项目所支持的样式

12、HTML Snippets 超级好用且初级的H5代码片段以及提示

13、Debug for Chrome 让vs code映射chrome的debug功能，静态页面都可以用vscode来打断点调试、配饰稍微复杂一点

14、Document this Js的注释模板

15、jQuery Code Snippets jquery提示工具

16、Html2jade html模板转pug模板

17、JS-CSS-HTML Formatter 格式化

18、Npm intellisense require 时的包提示工具

19、Open in browser 打开默认浏览器

20、One Dark Theme 一个vs code的主题

21、Path Intellisense 自动路径补全、默认不带这个功能

22、Project Manager 多个项目之间快速切换的工具

23、Pug(Jade) snippets pug语法提示

24、React Components 根据文件名创建反应组件代码。

25、React Native Tools reactNative工具类为React Native项目提供了开发环境。

26、Stylelint css/sass代码审查

27、Typings auto installer 安装vscode 的代码提示依赖库，基于typtings的

28、View In Browser 　默认浏览器查看HTML文件（快捷键Ctrl+F1可以修改）

29、Vscode-icons 让vscode资源目录加上图标、必备

30、VueHelper Vue2代码段（包括Vue2 api、vue-router2、vuex2）

31、Vue 2 Snippets vue必备vue代码提示

32、Vue-color vue语法高亮主题

33、Auto-Open Markdown Preview markdown文件自动开启预览

34、EverMonkey 印象笔记

35、atom one dark atom的一个高亮主题(个人推荐)

# 三、常用的电脑快捷键

1、ctrl + shift + delete 快速清除浏览器缓存

2、ctrl + alt + delete 快速进入任务管理器页面

3、window + L 快速锁定电脑

4、window + d 所有窗口最小化

5、 window + e 打开我的资源管理器(我的电脑)

6、 window + f 快速打开搜索窗口

7、 alt + tab 快速查看打开的应用与窗口
---
title: hexo搭建博客
cover: false
top: false
toc: false
mathjax: false
tags:
  - hexo
categories:
  - 工具
abbrlink: 23375
date: 2018-12-26 14:28:16
author:
img:
coverImg:
password:
summary:
keywords:
---
## 1、概念
Hexo是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。即把用户的markdown文件，按照指定的主题解析成静态网页。
**超快速度**
Node.js 所带来的超快生成速度，让上百个页面在几秒内瞬间完成渲染。

**支持 Markdown**
Hexo 支持 GitHub Flavored Markdown 的所有功能，甚至可以整合 Octopress 的大多数插件。

**一键部署**
只需一条指令即可部署到 GitHub Pages, Heroku 或其他平台。

**插件和可扩展性**
强大的 API 带来无限的可能，与数种模板引擎（EJS，Pug，Nunjucks）和工具（Babel，PostCSS，Less/Sass）轻易集成

## 2、安装
安装使用hexo**之前需要先安装Node.js（注意版本兼容）和Git**，当已经安装了Node.js和npm(npm是node.js的包管理工具，一般下载node会自带npm)。
```
$ npm install -g hexo-cli
```
可以通过以下命令在终端查看主机中是否安装了node.js和npm
```
$ node --version    #检查是否安装了node.js
$ npm --version     #检查是否安装了npm
$ git --version     #检查是否安装了git
```
## 3、建站
安装完Hexo之后，进入一个空文件夹，执行下列命令，Hexo将会在指定目录中新建所需要的文件，指定的目录即为Hexo的工作站
```
$ hexo init <folder>
$ cd <folder>
$ npm install
```
新建完成之后，指定目录中的情况如下

.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
3.1. _config.yml
网站的配置信息，您可以在此配置大部分的参数。 配置参数讲解

3.2. package.json
应用程序的信息，以及需要安装的模块信息。

3.3. scaffolds
模版文件夹。新建文章时，Hexo 会根据 scaffold 中的模板文件来建立新的文件。Hexo的模板是指在新建的markdown文件中默认填充的内容。例如，如果修改scaffold/post.md中的Front-matter内容，那么每次新建一篇文章时都会包含这个修改。也就是说，通过hexo命令每新建一个文章，都会包含指定模板文件中的内容。

官网模板详述

3.4. source
资源文件夹是存放用户资源的地方，如markdown文章。Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去。

注意：除 _posts 文件夹之外，开头命名为 _ (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。
3.5. themes
主题文件夹。Hexo 会根据主题来解析source目录中的markdown文件生成静态页面。官网主题详述

## 4、写作
假设我们的文章名为 “hello hexo markdwon”，在命令行键入以下命令即可：
```
$ hexo new "hello hexo markdown"
```
上述命令的结果是在 ./hexo/source/_posts 路径下新建了一个 hello-hexo-markdown.md 文件。


可以执行下列命令来创建一篇新文章。

$ hexo new [layout] <title>
可以在命令中指定文章的布局（layout），不指定默认为 post，也可以通过修改 _config.yml 中的 default_layout 参数来指定默认布局。创建的新文章会自动加上指定布局对应的模板文件中的内容。

4.1. 布局（Layout）
Hexo 有三种默认布局：post、page 和 draft，它们分别对应不同的路径，而自定义的其他布局和 post 相同，都将储存到 source/_posts 文件夹。

布局路径postsource/_postspagesourcedraftsource/_drafts

如果你不想你的文章被处理，你可以将 Front-Matter 中的layout: 设为 false 。

4.2. 模版（Scaffold）
在新建文章时，Hexo 会根据 scaffolds 文件夹内相对应的文件来建立文件，例如：

$ hexo new photo "My Gallery"
在执行这行指令时，Hexo 会尝试在 scaffolds 文件夹中寻找 photo.md，并根据其内容建立文章，以下是您可以在模版中使用的变量：

变量描述layout布局title标题date文件建立日期

4.3. Front-matter
Front-matter是文件最上方以 --- 分隔的区域，用于指定个别文件的变量，举例来说：
```
---
title: Hello World
date: 2013/7/13 20:46:25
---
```
注意：一般Front-matter使用的yaml语法，yaml语法需要注意空格，如title: Hello World冒号需要有一个空格，当然除YAML 外，你也可以使用 JSON 来编写 Front-matter。
以下是预先定义的参数，您可在模板中使用这些参数值并加以利用。

参数描述默认值layout布局title标题date建立日期文件建立日updated更新日期文件更新日期comments开启文章的评论功能truetags标签（不适用于分页）categories分类（不适用于分页）permalink覆盖文章网址

分类和标签

只有文章支持分类和标签，您可以在 Front-matter 中设置。在其他系统中，分类和标签听起来很接近，但是在 Hexo 中两者有着明显的差别：分类具有顺序性和层次性而标签没有顺序和层次。
···
categories:
- Diary
tags:
- PS3
- Games
···
WordPress支持对一篇文章设置多个分类，而且这些分类可以是同级的，也可以是父子分类。但是Hexo不支持指定多个同级分类。下面的指定方法： 
···
categories:
Diary
Life
···
会使分类Life成为Diary的子分类，而不是并列分类。因此，有必要为您的文章选择尽可能准确的分类.

4.4. 文章摘要
设置文章摘要，我们只需在想显示为摘要的内容之后添 <!-- more --> 即可。像下面这样：

---
title: hello hexo markdown
date: 2016-11-16 18:11:25
tags:
- hello
- hexo
- markdown
---
我是短小精悍的文章摘要(๑•̀ㅂ•́) ✧

<!-- more -->

紧接着文章摘要的正文内容

···
这样，<!-- more --> 之前、文档配置参数之后中的内容便会被渲染为站点中的文章摘要。

注意！文章摘要在文章详情页是正文中最前面的内容。

4.5. 资源引用
写个博客，有时候会想添加个图片或者其他形式的资源等等。有以下两种方式进行解决：

使用绝对路径引用资源，在 Web 世界中就是资源的 URL
使用相对路径引用资源
对于使用相对路径引用资源的，我们可以使用 Hexo 提供的资源文件夹功能。

使用文本编辑器打开站点根目录下的 _ config.yml 文件，将 post_asset_folder 值设置为 true。

post_asset_folder: true
修改之后会开启 Hexo 的文章资源文件管理功能。Hexo 将会在我们每一次通过 hexo new <title> 命令创建新文章时自动创建一个同名文件夹，于是我们便可以将文章所引用的相关资源放到这个同名文件夹下，然后通过相对路径引用。例如，你把一个 example.jpg 图片放在了这个同名文件夹中，使用相对路径的常规 markdown 语法 即可访问 。





## 5、网站发布
首先执行下列命令生成相应的静态网页，生成的静态网页以及相关资源都会在public目录下

$ hexo generate
5.1. 用hexo-server
hexo-server模块的主要命令如下，输入以下命令以启动服务器，您的网站会在 http://localhost:4000 下启动。在服务器启动期间，Hexo 会监视文件变动并自动更新，您无须重启服务器。

$ hexo server
如果您想要更改端口，或是在执行时遇到了 EADDRINUSE 错误，可以在执行时使用 -p 选项指定其他端口，如下：

$ hexo server -p 5000
但是个人认为此方式比较适合用于调试网站，并不适合长时间的网站服务器，同时为了让这个命令在后台长时间运行，需要编写相应的脚本。

5.2. 部署到Git上
安装软件：node.js 和 git
注册 gitee /github

1.在本地git绑定你的GitHub账号（会保存到本地的凭据管理器）或者通过SSL免密登录
```
$ git config --globale user.name "gitee空间地址"
$ git config --globale user.email "你的邮箱"
$ ssh-keygen -t rsa -C "你的邮箱"
```

2.配置根目录下的_config.yml
下载npm包：
npm install hexo-deployer-git --save

```
deploy:
- type: git
  repo: https://voidking.com/voidking/voidking.github.io.git  // 仓库地址
  branch: master    // 仓库分支
```


hexo d 上传

5.3. 部署到Apache或者Nginx上
通过hexo g命令生成的都是静态网页，可以把生成的public目录中的文件，全都拷贝到网站根目录，然后启动apache或者nginx服务。



## 6、其他基础命令
6.1. 清除缓存文件
为了避免不必要的错误，在生成静态文件前，强烈建议先运行以下命令：

$ hexo clean
上述命令会清除本地站点文件夹下的缓存文件（db.json）和已有的静态文件（public）。

## 7、引用资源(图片配置)
写个博客，有时候我们会想添加个图片啦 O.O，或者其他形式的资源，等等。

这时，有两种解决办法：

- 使用绝对路径引用资源，在 Web 世界中就是资源的 URL
- 使用相对路径引用资源

文章资源文件夹
如果是使用相对路径引用资源，那么我们可以使用 Hexo 提供的资源文件夹功能。

使用文本编辑器打开站点根目录下的 _ config.yml 文件，将 post_asset_folder 值设置为 true。
```
post_asset_folder: true
```
上面的操作会开启 Hexo 的文章资源文件管理功能。Hexo 将会在我们每一次通过 hexo new <title> 命令创建新文章时自动创建一个同名文件夹，于是我们便可以将文章所引用的相关资源放到这个同名文件夹下，然后通过相对路径引用。

**方式一：相对路径引用的标签插件**
通过常规的 markdown 语法和相对路径来引用图片和其它资源可能会导致它们在存档页或者主页上显示不正确。我们可以通过使用 Hexo 提供的标签插件来解决这个问题：
```
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}
```
比如说：当你打开文章资源文件夹功能后，你把一个 example.jpg 图片放在了你的资源文件夹中，如果通过使用相对路径的常规 markdown 语法 ![](/example.jpg) ，它将 不会 出现在首页上。（但是它会在文章中按你期待的方式工作）

！！！注意： 如果已经开启了文章的资源文件夹功能，当使用 MarkDown 语法引用相对路径下的资源时，只需 ./资源名称，不用在引用路径中添加同名文件夹目录层级。

正确的引用图片方式是使用下列的标签插件而不是 markdown ：
```
{% asset_img example.jpg This is an example image %}
```
通过这种方式，图片将会同时出现在文章和主页以及归档页中。

**方式二：插件**
```
npm install hexo-renderer-marked --save
```
这样我们通过 img 标签就可以引入图片了

！！！注意： 如果已经开启了文章的资源文件夹功能，当使用 MarkDown 语法引用相对路径下的资源时，只需 ./资源名称，不用在引用路径中添加同名文件夹目录层级。.!
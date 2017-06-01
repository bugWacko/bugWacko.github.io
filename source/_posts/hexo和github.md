---
title: hexo和github
date: 2017-05-24 16:20:48
tags: 

- HTML hexo
- HTML git
- HTML node.js

---

## 什么是HEXO？
Hexo是一个快速，简单和强大的博客框架。您在[Markdown](https://daringfireball.net/projects/markdown/)（或其他语言）中撰写帖子，Hexo会在几秒钟内生成具有美丽主题的静态文件。

+ <!-- more -->

## 安装
只需要几分钟的时间来设置Hexo。如果您遇到问题，找不到解决方案，请[提交一个GitHub问题](https://github.com/hexojs/hexo/issues)，我会尽力解决。

## 要求
安装Hexo很容易。但是，您需要首先安装几个其他的东西：
> * [Node.js](https://nodejs.org/en/)
> * [Git](https://git-scm.com/)

如果您的电脑已经有这些，恭喜！只需安装Hexo与npm：

```
$ npm install -g hexo-cli
```
## 安装Git
* Windows：下载并安装[git](https://git-scm.com/)。
* 苹果：与安装它[自制](https://brew.sh/)，[MacPorts](https://www.macports.org/)的或[安装程序](https://sourceforge.net/projects/git-osx-installer/)。
* Linux（Ubuntu，Debian）： sudo apt-get install git-core
* Linux（Fedora，Red Hat，CentOS）： sudo yum install git-core

## 安装Node.js
安装Node.js的最好方法是使用[nvm](https://github.com/creationix/nvm)。

```
$ curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | SH

```

```
$ wget -qO- https://raw.githubusercontent.com/creationix/nvm/master/install.sh | SH

```
安装nvm后，重新启动终端并运行以下命令来安装Node.js.

```
$ nvm install stable

```
或者，下载并运行[安装程序](https://nodejs.org/en/)。

## 安装Hexo

```
$ npm install -g hexo-cli

```
安装Hexo后，运行以下命令在目标中初始化Hexo `<folder>`

```
$ hexo init <folder>
$ cd <folder>
$ npm安装
```

## 目录结构
一旦初始化，您的项目文件夹将如下所示：

```
|-- _config.yml
|-- package.json
|-- scaffolds
|-- source
   |-- _posts
|-- themes
|-- .gitignore
|-- package.json
```

### 1. _config.yml
全局配置文件，网站的很多信息都在这里配置，诸如网站名称，副标题，描述，作者，语言，主题，部署等等参数。这个文件下面会做较为详细的介绍。具体如下

```
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: Hexo #网站标题
subtitle:   #网站副标题
description:  #网站描述
author: John Doe  #作者
language:    #语言
timezone:    #网站时区。Hexo 默认使用您电脑的时区。时区列表。比如说：America/New_York, Japan, 和 UTC 。

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://yoursite.com   #你的站点Url
root: /                       #站点的根目录
permalink: :year/:month/:day/:title/   #文章的 永久链接 格式   
permalink_defaults:    #永久链接中各部分的默认值

# Directory   
source_dir: source   #资源文件夹，这个文件夹用来存放内容
public_dir: public     #公共文件夹，这个文件夹用于存放生成的站点文件。
tag_dir: tags         # 标签文件夹     
archive_dir: archives    #归档文件夹
category_dir: categories      #分类文件夹
code_dir: downloads/code     #Include code 文件夹
i18n_dir: :lang                #国际化（i18n）文件夹
skip_render:                #跳过指定文件的渲染，您可使用 glob 表达式来匹配路径。    

# Writing
new_post_name: :title.md # 新文章的文件名称
default_layout: post     #预设布局
titlecase: false # 把标题转换为 title case
external_link: true # 在新标签中打开链接
filename_case: 0     #把文件名称转换为 (1) 小写或 (2) 大写
render_drafts: false  #是否显示草稿
post_asset_folder: false  #是否启动 Asset 文件夹
relative_link: false      #把链接改为与根目录的相对位址    
future: true                #显示未来的文章
highlight:                    #内容中代码块的设置    
  enable: true
  line_number: true
  auto_detect: false
  tab_replace:

# Category & Tag
default_category: uncategorized
category_map:          #分类别名
tag_map:            #标签别名

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD         #日期格式
time_format: HH:mm:ss        #时间格式    

# Pagination
## Set per_page to 0 to disable pagination
per_page: 10    #分页数量
pagination_dir: page  

# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: landscape   #主题名称

# Deployment
## Docs: https://hexo.io/docs/deployment.html
#  部署部分的设置
deploy:     
  type:  #类型，常用的git
```
	
### 2. package.json
hexo框架的参数和所依赖插件，如下：

```
{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "hexo": {
    "version": "3.2.0"
  },
  "dependencies": {
    "hexo": "^3.2.0",
    "hexo-generator-archive": "^0.1.4",
    "hexo-generator-category": "^0.1.3",
    "hexo-generator-index": "^0.2.0",
    "hexo-generator-tag": "^0.2.0",
    "hexo-renderer-ejs": "^0.2.0",
    "hexo-renderer-stylus": "^0.3.1",
    "hexo-renderer-marked": "^0.2.10",
    "hexo-server": "^0.2.0"
  }
}
```

### 3. scaffolds
scaffolds是“脚手架、骨架”的意思，当你新建一篇文章（hexo new 'title'）的时候，hexo是根据这个目录下的文件进行构建的。基本不用关心。

### 4. source
这个目录很重要，新建的文章都是在保存在这个目录下的.
_posts 。需要新建的博文都放在 _posts 目录下。

_posts 目录下是一个个 markdown 文件。你应该可以看到一个 hello-world.md 的文件，文章就在这个文件中编辑。

_posts 目录下的md文件，会被编译成html文件，放到 public （此文件现在应该没有，因为你还没有编译过）文件夹下。

### 5. themes
网站主题目录，hexo有非常好的主题拓展，支持的主题也很丰富。该目录下，每一个子目录就是一个主题，我的子目录如下：

```
|-- landscape  //默认主题
|-- hexo-theme-next  //第三方主题
```
你也可以自己下载主题放到该文件下,[hexo主题](https://hexo.io/themes/)

#### 如果大家想了解更多，可以到[Hexo官网](https://hexo.io/)查看

#### Happy reading!

---
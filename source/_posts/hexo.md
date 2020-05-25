---
title: hexo博客不同设备进行同步更新
categories: 操作归纳
tags: hexo
abbrlink: 53714
date: 2020-05-21 16:43:39
---
# 前言

之前在单位电脑上基于hexo+github page+coding搭建了自己的博客，之后面临着可能换电脑或者在家编辑博客的问题，所以想要对博客进行多端操作，参照了[知乎上的高赞答案](https://www.zhihu.com/question/21193762)，整体已完成。现将具体方法及碰到的问题做个总结归纳（以下所有内容只针对于github）。

<!-- more -->

## 环境说明 ##
* system：`windows 10`；
* npm：6.4.1；
* hexo：4.2.1；
* next：7.8.0；

## 1. 进行本地环境备份操作

首先要明确，hexo的文件目录结构，以及所有文件的作用，本机为window环境，我的目录信息如下
```
.── .deploy_git #编译后的文件
├── node_modules：是依赖包
├── public  #存放被解析markdown、html文件
├── scaffolds #当您新建文章时，根据 scaffold生成文件
├── source  #资源文件夹
|   └── _posts #博客文章目录
└── themes #主题
├── _config.yml   #网站的配置信息。标题、网站名称等
├── db.json：#source解析所得到的
├── package.json  # 应用程序的配置信息
└── package-lock.json #npm5.0以上新增的程序依赖版本信息
```
要注意`package-lock.json`这个文件，后续有个坑就是因为它。
其中我们上传到github的文件是hexo编译后的文件，用来生成网页，并非是源文件。主要内容在`.deploy_git`内，其他文件都没有进行上传。想要保证其他电脑能够进行编辑操作，我们需要把源文件上传到github上，其中有两种方式：
1. 另建一个代码仓库，进行文件上传。
2. 在同一个仓库下创建一个分支。   


第一种虽然更规则一些，但个人感觉不是很方便，所以我选择的是第二种。
### 1-1. 创建分支
首先我们需要在github仓库新建一个分支：
![](branch-picture.jpg)
在其中输入分支名称`hexo`，直接回车即可创建。
### 1-2. 分支设置

上一步操作已经创建好了环境存储分支，但当前的仓库默认分支仍为`master`，所以在`hexo`分支创建完成后，需要将其设置为默认分支。切换到该`hexo`分支，并在该仓库`->Settings->Branches->Default branch`中将默认分支设为`hexo`，`update`保存：
![](setting-picture.jpg)
### 1-3. 上传配置文件

此步操作需在原电脑端进行
#### 1-3-1. 克隆`hexo`分支  ####
* 首先创建一个克隆存放目录如`_branches_hexo`，在该文件夹下进入`git bash here`命令行，执行：
```
git clone git@github.com:yourname/yourname.github.io.git
```
克隆到本地后，`cd username.github.io`进入到该文件目录   
* 在当前目录执行`git branch`命令查看当前所在分支，应为新建的`hexo`。
```
$ git branch
*hexo
```
#### 1-3-2. 上传部署文件 ####
* 进入到克隆分支仓库`_branches_hexo`，将`.git`文件夹复制到原博客文件目录下。如果未找到，请在查看中将`隐藏的文件夹`勾选上。
* 删除掉主题下的`.git`文件夹，原因：git不能嵌套上传。
* 删除掉克隆的`_branches_hexo`仓库。
* 在原博客文件目录下，进入`git bash here`命令行，按顺序执行上传命令：
```
git add . 
git commit -m "上传备注" 
git push origin hexo 
```
##### 问题及解决思路 #####
此处上传到github上时，由于`npm`5及以上版本会自动创建一个`package-lock.json`文件， `package-lock.json`文件中已经记录了整个所在文件夹的树状结构，版本号，甚至连模块的下载地址都记录了。
```
{
"name": "test_pkg_lock",
"version": "1.0.0",
"lockfileVersion": 1,
"dependencies": {
"commander": {
"version": "2.9.0",
"resolved": "https://registry.npmjs.org/commander/-/commander-2.9.0.tgz",
"integrity": "sha1-nJkJQXbhIkDLItbFFGCYQA/g99Q="
},
"cssfilter": {
"version": "0.0.8",
"resolved": "https://registry.npmjs.org/cssfilter/-/cssfilter-0.0.8.tgz",
"integrity": "sha1-ZWTKzLqKdt2bS5IGaLn7f9pQ5Uw="
},
"graceful-readlink": {
"version": "1.0.1",
"resolved": "https://registry.npmjs.org/graceful-readlink/-/graceful-readlink-1.0.1.tgz",
"integrity": "sha1-TK+tdrxi8C+gObL5Tpo906ORpyU="
},
"xss": {
"version": "0.2.18",
"resolved": "https://registry.npmjs.org/xss/-/xss-0.2.18.tgz",
"integrity": "sha1-bfX7XKKL3FHnhiT/Y/GeE+vXO6s="
}
}}
```
增加了日后我们更新依赖或重新安装的下载速度。但`github`对我们上传的文件做了漏洞扫描及智能分析处理，所以`package-lock.json`文件中部分版本会被提示高风险警告。正确的做法是按照其提示进行版本升级，但由于`hexo`整体版本依赖要求较高，所以我的做法是忽略掉`github`的安全警告。
![](security-picture.jpg)
在标识位置点选相关的操作，我这是已经选完的截图。

此时，我们已完成`hexo`博客本地原部署环境的上传备份操作。之后则需要我们在新环境中进行操作。
## 2. 新环境的部署编辑操作 ##
相当于初步的搭建了`hexo`环境。
* 安装git：   
[git官方地址](https://git-scm.com/download)
* 安装node.js：
[node.js官方地址](https://nodejs.org/en/)
* 设置git全局邮箱和用户名：
```
git config --global user.name "yourgithubname"
git config --global user.email "yourgithubemail"
```
* 设置ssh key：
```
ssh-keygen -t rsa -C "youremail" #生成后填到github和coding上（有coding平台的话） 
#验证是否成功 
ssh -T git@github.com 
ssh -T git@git.coding.net #(有coding平台的话)
```
* 安装hexo：   
```
npm install hexo-cli -g
```

* 在所选文件夹下克隆github上的配置环境文件：
````
git clone git@github.com:yourname/yourname.github.io.git
````

* 进入到克隆文件夹下：
```
cd xxx.github.io
npm install
npm install hexo-deployer-git --save
```
* 生成，部署：
```
hexo g 
hexo d
```
##### remark #####
1. 在不同客户端编写博客前，需要执行
```
git pull
```
拉取命令来保证版本一致。

2. 写完博客后，请先提交源文件到hexo分支后：
```
git add . 
git commit -m "上传备注" 
git push origin hexo 
```
再执行`hexo d`上传操作。
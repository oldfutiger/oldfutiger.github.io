---
title: hexo博客不同设备进行同步更新
date: 2020-05-21 16:43:39
categories: 操作归纳
tags: hexo
---
# 前言

之前在单位电脑上基于hexo+github page+coding搭建了自己的博客，之后面临着可能换电脑或者在家编辑博客的问题，所以想要对博客进行多端操作，参照了[知乎上的高赞答案](https://www.zhihu.com/question/21193762)，整体已完成。现将具体方法及碰到的问题做个总结归纳（以下所有内容只针对于github）。
<!-- more -->

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
![](branch-prcture.jpg)
在其中输入分支名称`hexo`，直接回车即可创建。
### 1-2. 分支设置

上一步操作已经创建好了环境存储分支，但当前的仓库默认分支仍为`master`，所以在`hexo`分支创建完成后，需要将其设置为默认分支，切换到该`hexo`分支，并在该仓库`->Settings->Branches->Default branch`中将默认分支设为`hexo`，`update`保存：
![](setting-picture.jpg)
### 1-3. 上传配置文件

此步操作需在原电脑端进行
#### 1-3-1. 克隆`hexo`分支  ####
* 首先创建一个克隆存放目录如`_branches_hexo`，在该文件夹下进入`git bash here`命令行，执行`git clone git@github.com:yourname/yourname.github.io.git`。






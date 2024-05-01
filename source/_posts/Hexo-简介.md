---
title: Hexo 简介
date: 2024-04-30 20:37:16
tags:
---

# 在Github上部署Hexo

**简述**：用Hexo本地创建Blog，然后将博客部署到Github上

- **安装**

  安装git、npm

  安装Hexo

  ​	npm install -g hexo-cli

  进阶安装和使用

  ​	局部安装hexo包 npm install hexo

  ​	然后可以使用hexo 命令

  

- **创建Hexo项目**

  ​	hexo init <folder>

  ​	cd <folder>

  ​	npm install



# 创建SSH Key

- **创建**

​	命令行执行 

```
    ssh-keygen -t ras -C "yourmail@xxx.com"
```

​	**注意：**邮箱地址是你github账号关联的邮箱，配置的目的是将github仓库和本地机器连起来，这样可以将文件提交到仓库，不需要账号密码。进入github的对应仓库，点击‘setting’进入设置，点击“SSH and GPG key"，点击”New SSH Key", 将前面生成的c:/users/用户名/.ssh/目录下的id_ras.pub内容	复制过去，title 随便取，点击保存，至此本地与github之间连接就配置好了。
​	**测试：**
   	运行ssh -T git@github.com



# 配置git账号邮箱

git config --global user.name "github账号"
git config --global user.email "github账号关联的邮箱"

- 
  创建github仓库目录

  ​	mkdir myblob

  ​	git clone xxxxx.git

  

- 初始化hexo

  ​	cd myblob

  ​	hexo init

  

- 查看初始文件  

  ​	hexo g

  

- 启动服务 

  ​	hexo s  

​	服务会监听本地4000端口，可以访问http://localhost:4000



# 上传blog到github

打开根目录下的*_config.yml中的deploy部分 \blog\_config.yml
修改：
deploy:
  type: git
  repository: https://github.com/Jerrywang2013/Jerrywang2013.github.io.git
  branch: main

上传： hexo clean && hexo deplot
如果出现 ERROR Deployer not found: git w安装插件： npm install hexo-deployer-gi
浏览<Github用户名>.github.io检查网站是否运作

# hexo使用

- 修改主题

  ​	npm install hexo-theme-volantis

  

- 添加文章

  ​	hexo new 英语测试

​		进入source\__posts\英语测试.md 下编写具体内容即可



- 生成文件

  ​	hexo generate

  

- 启动服务器

  ​	hexo server 

  可选参数

  ​        -p --port 监听端口

  ​        -s --static 只使用静态文件

  ​        -l --log 启动日记记录，使用覆盖记录格式

  

- 部署

  ​	hexo deploy

  ​	-g --generate 部署之前预先生成静态文件

  

- 渲染

  ​	hexo render <file1> <file2>...

  ​	 -o --output 设置输出路径

  

- 清除缓存

  ​	hexo clean

  ​	清除缓存文件(db.json)和已生成的静态文件(public)

  ​	在某些情况下（更换主题后）如果发现对站点的更改无论如何也不生效，可以尝试此命令。

  

- 列出网站数据

  ​	hexo list <type>

  

- 显示版本

  ​	hexo version

  

- 列出网站配置

  ​	hexo config [key] [value]

  ​	列出网站的配置(_config.yml)，如果指定了key，则只展示对应key的值，如果同时指定key和value，则将此key值修改为value。

  

- 调试模式

  ​	hexo --debug

  

- 简洁模式

  ​	hexo --silent



# 参考

**markdown基本语法**
https://www.cnblogs.com/nickchen121/p/10821946.html

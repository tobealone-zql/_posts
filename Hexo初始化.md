---
title: Hexo初始
description: # 描述
tags: 
  - Hexo
---

暂无说明

<!-- more -->

###  环境准备

- [Node.js](https://nodejs.org/en/)安装
- [Git](https://git-scm.com/)安装

- 实例GitHub号

````
用户名：ergouzi
账号(注册邮箱): 55252662@qq.com
````



###  Hexo安装

````
npm install -g hexo-cli
# 选择一个安装位置进行初始化安装  myhexo为生成的hexo文件夹名称
hexo init myhexo
cd myhexo
npm install
````

###  Next安装

```
cd myhexo/
git clone https://github.com/next-theme/hexo-theme-next themes/next
```

###  使用Next主题

````
# 修改myhexo目录下_config.yml配置
theme: next
````

###  配置GitHub

```
1> 创建GitHub账号
2> 创建GitHub仓库，仓库命名格式固定,不可修改，格式为：GitHub用户名.github.io   例：ergouzi.github.io
```

###  Git生成密钥 绑定GitHub

````
git config --global user.name "你的GitHub用户名"   
git config --global user.email "你的GitHub注册邮箱"
# 生成ssh密钥文件
ssh-keygen -t rsa -C "你的GitHub注册邮箱"
# 打开GitHub_Settings_keys页面[https://link.zhihu.com/?target=https%3A//github.com/settings/keys] ，新建new SSH Key
````

###  保存站点信息

````
# 修改myhexo目录下_config.yml配置
deploy: 
  type: 'git'
  repo: git@github.com:ergouzi/ergouzi.github.io.git
  branch: master

````

###  安装部署插件

````
npm install hexo-deployer-git --save
````


###  访问网址

````
ergouzi.github.io
````

###  设置图片引用

````
# 修改myhexo目录下_config.yml配置 创建新的博客后会生成同名文件夹
post_asset_folder: true
# 下载插件
npm install https://github.com/7ym0n/hexo-asset-image --sa
# 图片引用格式
![xxx](xxx/xxx.png)
````

###  优化

````
# 修改myhexo目录下_config.yml配置 改为中文
language: zh-Hans
````
###  部署

```
hexo clean && hexo g && hexo d
```



## 另：

​       本文未能细致到傻瓜式操作的程度，有些细节还是要参考其他文章.



**教程参考:**

​				https://suyin-blog.club/2020/323EH2Y/

​                https://zhuanlan.zhihu.com/p/26625249

​                https://blog.csdn.net/science_Lee/article/details/84633237




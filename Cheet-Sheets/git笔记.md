# git笔记

[TOC]

## 第一次使用怎么配置git

-['Git教程'](https://blog.csdn.net/qq_36667170/article/details/79085301)

-['Git SSH配置'](https://blog.csdn.net/qq_36667170/article/details/79094257)

## 常用语法

cd:  转到路径(路径中符号为/)
pwd: 当前目录
ls: 当前文件夹里有什么
cd ..：返回上一级
mkdir +文件夹名字：新建文件夹
touch +文件名 ：只能新建文件
rm 文件名.文件类型 ：删除文件
rm -r 文件夹： 删除文件夹
git log: 查看更改记录

## 添加远程仓库

1. 复制SSH
2. 在本地仓库打开gitbash, 输入：$ git remote add + 名字 + 连接地址  
注: 【连接地址】就是SSH中的git@****；【名字】自己起个名字，本地仓库会连接多个远程仓库，名字用于区分。
3. 确认是够成功添加库：$ git remote -v
如果出现一个push和一个fetch即为成功。

## 文件上传

1. 本地新建一个文件。
2. $ git add -A 将其添加到缓存区
3. $ git commit -m "修改注释" 给刚才添加的东西加一个备注。（将当前暂存区的文件实际保存到仓库的历史记录中）
4. $ git push -u 仓库名称 分支  
注: 【仓库名称】: 连接到哪个仓库;【分支】: master。如果有合作项目,只添加自己的分支。

## 文件下拉

$ git pull 仓库名称  可以用于在github中进行修改, 然后使用该命令更改本地。

注: 用于从远程仓库获取最新的更改并合并到当前分支。等价于git fetch(拉取远程数据)+git merch(合并数据)

## 文件克隆

$ git clone 加上SSH地址

得到的是一个文件夹，想放在哪里就数据上面一行命令。

再用$ git remote -v检查一下是否成功。

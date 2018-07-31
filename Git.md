Git常用命令：

提交到GitHub：

git status：查看当前目录状态

git add 文件名：将文件提交到暂存区

git add .：提交所有修改、新增的文件到暂存区

git commit -m "备注"：提交暂存区的文件到本地库

git push origin master：提交暂存区的文件到github



Git相比SVN：

SVN，有单点故障的隐患

Git，会把所有版本操作的记录，都存在本地。

​	每次提交都会新增加文件，而不是对之前的进行操作。

Git结构：

![git结构图.png](https://github.com/g453030291/building-java-tower/blob/master/images/git结构图.png)

Git分为两种工作方式：

本团队协作

![本团队协作.png](https://github.com/g453030291/building-java-tower/blob/master/images/本团队协作.png)

外部人员协作

![外部团队协作.png](https://github.com/g453030291/building-java-tower/blob/master/images/外部团队协作.png)



Git命令行操作：

本地库操作

①：本地库初始化

git init：进行git本地库初始化，生成.git文件。

git add：

git rm --cache 文件名：文件从暂存区撤销

git log：查看日志（空格翻页）

git log --pretty=oneline：格式化日志信息，一行显示

git log --oneline：只能查看当前版本之前的版本记录

git reglog:（可以查看所有的版本记录）（常用）

cat 文件名：查看文件内容

②：

远程库操作

设置签名：（两种级别）

git config (user.name XXX/user.email XXX@qq.com)项目/仓库级别：仅在当前目录有效（二种都存在时项目级别优先级更高）

git config --global (user.name XXX/user.email XXX@qq.com)系统用户级别：登录当前操作系统的用户范围

③：git进行版本管理，原理就是移动一个叫HEAD的指针

**④：git进行版本控制**

第一种：

**先用git reflog查看所有log记录**

**git reset --hard "索引值"：把HEAD指针移动到到索引值指定的版本**

第二种：(^)

git log --oneline(只显示历史版本)

git reset --hard HEAD^(N个^表示回退N个版本(只能往后回退，不能往前))

第三种：(~)

git log --oneline

git reset --hard HEAD~3(3表示回退3步，N表示回退N步)

第四种：（移动本地库的HEAD指针，工作区、暂存区都会刷新，working tree clean）（这个命令可以恢复已经提交到暂存区，还没有提交到本地库，但是工作区被删除的文件）

git reset --hard HEAD

⑤：

(查看工作区某个文件状态)cat 文件名（表示查看某个文件）

（查看暂存区状态）git status

⑥：

查看所有分支：git branch -v

创建分支：git branch 分支名

切换分支：git checkout 分支名

合并分支：

1.切换到接受修改的分支上

2.git marge 分支名

(合并冲突的时候会进入marging的状态，修改完冲突后，使用git add 、git commit提交到本地库。注：使用git commit时，只能带注释，**不能在后面加上文件名**)

⑦：git remote -v：查看所有保存的git hub远程地址

git remote add 别名 https://github.com/XXXX/XXXXX.git

⑧：推送到远程库:git push origin master

⑨：克隆到本地项目：git cloen https://github.com/XXXX/XXXXX.git(不需要初始化本地库，直接cloen)

⑩：pull=fetch+merge 

pull:拉取远程库更新并合并到本地

fetch：拉取新内容

merge：合并新内容


# git命令
### 1.git 安装及版本查看

+ git 的平台安装地址为：[http://git-scm.com/downloads](http://git-scm.com/downloads)


+ 查询git是否安装成功可以使用

  `
  git --version
  `


+ 配置git的个人用户名称为 runoob

  `git config --global user.name "runoob"
  `


+ 配置git的电子邮件地址为 test@runoob.com

  `git config --global user.email test@runoob.com
  `


*根据自身情况配置用户名和邮箱*


+ 查看git的配置信息：

  ` git config --list
  `

### 2.创建本地仓库

+ 初始化当前文件目录为本地仓库

  ` git init
  `


+ 指定目录newGit为git仓库

  `git init newGit`

### 3.克隆远程仓库

+ 克隆远程仓库地址“http:xxxxx” 到当前终端路径下

  ` git clone http:xxxxx
  `


+ 克隆远程仓库地址“http:xxxxx” 到当前终端路径下的gitDemo文件下
  *gitDemo是终端创建的*

  `git clone http:xxxxx gitDemo
  `

### 4.创建分支和切换分支

+ 创建分支名为new-branch

  `git branch new-branch`


+ 切换分支到分支 new-branch

  `git checkout new-branch`


### 5. 文件状态查看和更改

+ 文件的状态查看

  `git status`


+ 将有内容或路径变更的文件README.md添加到暂存区

  `git add README.md`


+ 将所有有内容或路径变更的文件添加到暂存区

  `git add .`


+ 将处于暂存区和工作区中的文件README.md从暂存区和工作区中删除

  `git rm README.md`


+ 强制将处于暂存区和工作区中的文件README.md从暂存区和工作区中删除

  `git rm -f README.md`


+ 将处于暂存区文件README.md从暂存区中移除，但仍保留在工作区中

  `git rm --cached README.md`


+ 将暂存区的文件提交并记录此次变更动向

  `git commit -m '自定义此次文件主要变更主题'`
  

### 6. git pull/ git push

+ git pull 命令用于从远程获取代码并合并本地的版本
  git pull 合并原程仓库origin 分支master 到本地仓库分支 new-branch

  `git pull origin master:new-branch`
  

+ git push 命令用于从将本地的分支版本上传到远程并合并
  git push  本地仓库locaOrigin的分支new-branch 上传到远程仓库分支 master 并合并

  `git push localOrigin new-branch:master`

### 7. 远程仓库的相关信息和添加/删除/重命名

+  列出当前仓库中已配置的远程仓库

    `git remote`


+  列出当前仓库中已配置的远程仓库，并显示它们的 URL

    `git remote -v`


+ 添加一个新的远程仓库名为origin且URL为“https://github.com/user/repo.git”，并添加到当前仓库中

  `git remote add origin https://github.com/user/repo.git`


+ 重命名已配置的远程仓库origin 为 new-origin

  `git remote rename origin new-origin`


+ 从当前仓库中删除指定的远程仓库new-origin

  `git remote remove new-origin`


+ 修改指定远程仓库的 URL 为“https://github.com/user/new-repo.git”

  `git remote set-url origin https://github.com/user/new-repo.git`


+ 显示远程仓库origin的详细信息，包括 URL 和跟踪分支

  `git remote show origin`

### 8. 使用终端添加内容为“this is test”的文件REMADE.md

    `echo "this is test" >> REMADE.md`

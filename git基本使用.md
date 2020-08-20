## git 基本使用
一个分布式版本系统，用于版本管理和多人协同开发
***
### 安装配置
安装过程没什么好说的，就是到官网下载合适的版本，选择一路点点点就可以了。<br>
安装完成后可以打开cmd窗口查看
```
git --version
//如果有版本显示则说明安装成功

//接着配置
git config --global user.name "用户名"
git config --global user.email "用户邮箱"
git config --l  //可以查看信息是否正确
```
### git架构
可以分为工作区，暂存区和版本库。<br>
工作区就是平时写代码的目录<br>
暂存区和版本库都在.git目录下，被git管理<br>
后续可以进一步深入了解

### git基本使用
* git init 
  * 在当前目录下生产一个.git目录，作为git仓库
* git status
  * 查看当前状态
* git add
  * 将工作区的文件放到暂存区中
  * git add . （暂存所有文件）
* git commit
  * 将暂存区中的内容放到版本去中
  * git commit -m/-message "描述信息"
    * 给当前版本添加一个描述信息
* git diff
  * 用来查看git status的详细信息
* git log
  * 用来查看日志，完整版
  * git log -oneline 简易版
* git reset --hard HEAD^
  * 回退到上一个版本，HEAD表示当前版本，HEAD^表示上一个版本，HEAD^^表示上上一个版本，还可以这样写HEAD~100
* git reset HEAD
  * 用于取消已经缓存的内容，如：git reset HEAD hello.php ，取消了对hello.php的缓存


### git 和 github 关联
1. 首先在本地生成ssh秘钥,使用：ssh-keygen -t rsa -C "youremail@example.com"
2. 在相应的用户目录的id_rsa.pub复制里面的key
3. 在github用户的ssh key设置中添加生成的key
4. 在本地输入：ssh -T git@github.com，如果出现You've successfully authenticated, but GitHub does not provide shell access.则说明连接成功。
   
### 提交本地仓库到github
关联成功后就比较简单了。
* 可以先在github上创建一个空白仓库，会出现以下提示
<img src='https://www.runoob.com/wp-content/uploads/2015/03/1BCB4379-1A25-4C77-BB82-92B3E7185435.jpg'
alt="创建成功提示，可以根据提示进行下一步提交">

* 如果本地已经有仓库了，可以直接使用，就可以提交成功了
* git remote add origin + git地址 （这个git地址可以在提示里看到）
* git push -u origin master

### 查看当前的远程仓库（github）
```
git remote 
或者 git remote -v 可以看到详细的链接地址
```

### 提取远程仓库（github）--关联后
1. 从远程仓库下载新分支和数据：git fetch
2. 将数据合并到当前分支：git merge

另一种是直接：git pull origin master<br>
可以简单粗暴地理解为：git pull origin master = git fetch + git merge

### 推送到远程仓库（github）
还是一样的操作
1. git add 文件/.   git到暂存区
2. git commit -m "描述"  git到本地仓库
3. git push origin master git到远程仓库，就是github上

### 另一种关联远程仓库的方法
首先我们可以了解一下怎么获取别人的远程仓库。<br>
* 进入别人的仓库页面后，可以看到一个clone or download的选项
* 复制给定的地址，ssh或者https都可以，但是ssh会快一点
* 在本地执行：git clone 复制的地址
可以这样下载别人的代码进行学习，但是这是别人的仓库，所以不能直接push。
<br>

那么我们可以用git clone的方法，关联一个远程仓库

* 在github上创建一个空仓库
* 复制创建完毕的创库的ssh或者https地址
* 在本地执行：git clone 达到关联。

### 分支管理
使用分支可以在不影响开发主线的基础上进行其他工作，是一个版本控制的重要工具。<br>
可以这样理解,版本控制就像一个链表，每一个版本都是一个节点，而每一个分支则是指向节点的一个
指针。

* git branch
  * 查看分支，仓库默认只有master分支，每次commit总会在master上记录新的版本。
* git branch BranchName
  * 创建分支，如：git branch dev，创建一个dev的分支
* git checkout BranchName
  * 切换分支。注意，创建后当前分支还是master，可以在git branch中看到，有标识的是当前分支
* git log
  * 历史记录
  * git log --online
  * git log --online --graph  用图像的形式表现出来
* git branch -d BranchName
  * 删除分支
* 合并分支
  * git merge BranchName，将该分支合并到当前分支中
  * 分为快速合并和三方合并
  * 快速合并：master主分支没有改变，所以这时只要将master分支的指针移动到新的指针节点即可，合并信息中会出现fast-foward的提示
  * 三方合并：当master和其他新的分支都有修改时，这时的合并成为三方合并。
  
### 解决冲突
不同的分支可能对同一文件进行了修改，合并时git无法判断，则会出现冲突。<br>
冲突的文件中会保留两个分支的代码，进行取舍后再次提交就可以顺利解决冲突。
1. 对冲突的内容进行取舍
2. 重新提交：git add .  
3. git commit 

  
### 配置vs code中的git
只要将git安装目录下cmd中的git.exe的路径完整复制下来，配置到vs code中的git.path，注意对"\"会有转义，可以用以下的方式解决。<br>
```
　"git.path":"D:/Git/cmd/git.exe"//方法一
　"git.path":"D:\\Git\\cmd\\git.exe"//方法二
```
配置成功就可以顺利使用了

### 参考资料：
<a href='https://www.runoob.com/git/git-tutorial.html'>菜鸟教程</a>
<a href="https://www.jianshu.com/p/296d22275cdd">Git和GitHub使用教程</a>



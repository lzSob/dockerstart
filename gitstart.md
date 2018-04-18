#### 公司git使用流程
概念：
	- 工作区:  改动
	- 暂存区： 存入cache的改动
	- 本地仓库： commit后存入本地仓库
	- 远程仓库： git push origin develop
	- commit-id:  每次commit操作都会生成，唯一id

大概流程：
1. 在github上fork dlink的代码仓库生成自己的代码仓库
2. 拉取自己仓库的代码到本地进行修改
3.  修改完成后push代码之前先同步dlink的代码，有冲突就解决冲突
4.  上传到自己的代码仓库
5.  执行pull request操作


下面这些命令就不讲了。
git clone
git pull
git add
git commit   #commit-id
git branch 
git push

##### 怎么同步dlink仓库的代码？
 [Configuring a remote for a fork](https://help.github.com/articles/configuring-a-remote-for-a-fork/) [syncing-a-fork](https://help.github.com/articles/syncing-a-fork/)这是官方文档说明  大概步骤如下：
* 查看远程库配置
```
$ git remote -v
# origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
# origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
``` 
*  添加一个远程仓库
```
$ git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git
```
然后再看
```
$ git remote -v
# origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
# origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
# upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (fetch)
# upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (push)
```
*  然后就可以开始同步代码了， 虾面的命令就是会每天都用到的
```
$ git fetch upstream
```
然后切换到你本地需要同步的分支， 比如 develop
```
$ git checkout develop
```
如果是个不存在 develop分支，可以使用 -b
```
$ git checkout -b develop
```
然后就同步啦
```
$ git merge upstream/develop
```
然后你本地代码就已经和那边代码一样了。 然后push到你的线上仓库。
```
$ git push origin develop
```
然后就完事了



可以查看文件的修改纪录，也会常用到。webstorm 右键 -> git -> annotate有同样的效果
```
git blame package.json 
```

放弃工作区
```
git checkout <file-name> #放弃单个文件改动
git checkout . #放弃所有改动
```
添加到暂存区的一些操作
```
git add -A
git rm --cached [file path]
git rm --f [file path]
```
查看log.   
```
git log
```
diff 对比不同
```
git diff #工作区和暂存区不同
git diff <commit-id> <commit-id> #任意两个commit之间的变化
git diff --cached #输出暂存区和本地最近的版本(commit)的different(不同)。
```

这些我也就不详细介绍了。  特定情况才用
```
git revert   #回滚相关
git tag   #标签相关
git stash #储藏
```





----

###### 设置ssh key
 公司项目是私有的， 拉取需要验证权限。 使用用户名＋密码的方式显得很繁琐，设置ssh key可以简化很多操作

会的就不用看了。 windows 使用git终端
```
cd ~/.ssh
ssh-keygen -t rsa -C "your_email@youremail.com"
```
 使用命令一直回车到底就好了，然后就可以看到👇这样
```
$ ls
id_rsa		id_rsa.pub	known_hosts
```
然后把你生成的公钥丢进你github账号就可以了
大概是这里https://github.com/settings/ssh/new

----
附上我平常查阅的git相关的[地址](https://github.com/521xueweihan/git-tips)







 


 

$\color{#4285f4}{G}\color{#ea4335}{o}\color{#fbbc05}{o}\color{#4285f4}{g}\color{#34a853}{l}\color{#ea4335}{e}$
觉得不清晰的，可以看[git教程](https://www.yiibai.com/git/git_pull.html)来学习。
### Git的下载
[Git官网](https://git-scm.com/)
![下载这个](https://github.com/rootnet-devlop-group/technical_investigation/blob/master/snapshot/git/Git%E4%B8%8B%E8%BD%BD.jpg)

### Git的基本命令
**1：创建项目**
1. 首先要在Github上创建一个仓库，copy他的仓库地址。
2. 在本地PC上找到你要上传Git上的项目的文件夹。
3. 执行下面的指令
```
git init    //初始化本地Git仓库
git add *   //将要上传的文件添加到上传序列中
git add .gitignore //为了防止你将不想提交的文件提交到Github上，比如.class文件
git commit -m "first commit"  //注意每次提交都需要有说明，这是将要上传的文件提交到你本地仓库。
git remote add origin https://github.com/rootnet-devlop-group/****.git //你刚才创建的仓库的地址，将本地仓库与远程仓库挂载在一起。
git push -u origin master //将你本地仓库多次提交的内容，同步到远程Github仓库中。


PS: 这是因为第一次建库所以相对比较繁琐。等到下次提交就是普通的add commit 和push操作了。

git pull //同步

```
一般常用的java系 git ignore的内容如下
```
# Compiled class file
*.class

# Log file
*.log

# BlueJ files
*.ctxt

# Mobile Tools for Java (J2ME)
.mtj.tmp/

# Package Files #
*.jar
*.war
*.nar
*.ear
*.zip
*.tar.gz
*.rar

# virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml
hs_err_pid*
```
然后将整个项目push到Github上，第二种是在Github上创建一个仓库，然后将本地链接挂载到这个仓库上，然后将项目push上来。

>就拿当前项目为例子https://github.com/rootnet-devlop-group/technical_investigation.git

git remote add origin https://github.com/rootnet-devlop-group/adsf.git

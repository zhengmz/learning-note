记录git相关的问题
=====

0. 一些有用的链接
-----
  * [A tutorial introduction to git](https://www.kernel.org/pub/software/scm/git/docs/v1.4.4.4/tutorial.html)
  * [Git User’s Manual](https://www.kernel.org/pub/software/scm/git/docs/user-manual.html)
  * [使用 Git 管理源代码](http://www.ibm.com/developerworks/cn/linux/l-git/)

1. svn迁移到git乱码问题解决
-----
来源：http://guoyunsky.iteye.com/blog/1845303

  公司之前使用svn进行代码管理,现在需要迁移到git。迁移到git很顺利，只需要安装git-svn即可。但迁移之后发现运行git log命令后，之前的那些commit消息都是乱码,花了点时间搞定了.网上没有搜到类似的资料,这里记录下,希望可以帮到有需要的人。
 
  这里以svn工程地址为 svn://192.168.1.1/my-project为例
### 1)首先需要安装git-svn：
  Ubuntu下很简单，运行命令:

    sudo apt-get install git-svn

### 2)svn代码迁移到git(有乱码)：
  可以通过一句命令就搞定:

    git svn clone svn://192.168.1.1/my-project/

  因为就是这样一条命令,发现会有乱码。所以采用下面的方案
 
### 3)svn代码迁移到git(无乱码)：

    #创建工程目录  
    sudo mkdir /tmp/my-project.git  
    cd /tmp/my-project.git  
      
    #初始化git  
    git svn init svn://192.168.1.1/my-project/  
      
    #设置编码和文件名允许中文等字符  
    git config --global core.quotepath false         #设置文件名允许中文等字符  
    git config --global gui.encoding utf-8           #设置git界面编码  
    git config --global i18n.commitencoding utf-8    #设置git commit时的编码  
    git config --global i18n.logoutputencoding utf-8 #设置git log输出时编码  
    export LESSCHARSET=utf-8  
      
    #获取代码  
    git svn fetch


2.git的一些设置和辅助功能
-----
来源：http://guoyunsky.iteye.com/blog/1841509

好记性不如烂笔头,一一记下吧. 方便自己也希望能够方便到别人
 
### 1)用户设置

    git config --global user.name "username"            #设置姓名
    git config --global user.email "username@email.com" #设置邮件

### 2)颜色设置

    git config --global color.diff auto                 # git diff的时候要显示颜色
    git config --global color.status auto               # git status的时候要显示颜色
    git config --global color.branch auto               # git branch的时候要显示颜色
    git config --global color.ui true                   # 把颜色的选项全部打开

### 3)命令别名设置

    git config --system alias.st status      # git status命令别名为git st
    git config --system alias.ci commit      # git commit命令别名为git ci
    git config --system alias.co checkout    # git checkout命令别名为git co
    git config --system alias.br branch      # git branch命令别名为git br

### 4)git 乱码解决

    git config --global core.quotepath false        #文件名不乱码,比如文件名是中文
    git config --global gui.encoding utf-8          #设置git gui的界面编码为UTF-8
    git config --global i18n.commitencoding utf-8   #设置 git commit -m时commit log 提交时使用 utf-8 编码
    git config --global i18n.logoutputencoding gbk  #设置git log输出日志时的编码是GBK

### 5)显示分支

修改后可以直关的看到你当前所处在哪个分支,无需通过git branch察看.需要在你的环境变量中如~/.bashrc文件中加入如下内容:

    parse_git_branch() {  
        git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(git::\1)/'  
    }  
    export PS1="${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]: \w \[\033[31m\]\$(parse_git_branch)\[\033[00m\]$\[\033[00m\] "  

  之后再source ~/.bashrc之后,你再进入到一个git目录就能看到所在分支了,类似如下:

    /tmp/demo (git::master)$

3. 其他问题
-----

### 1)git pull或push时总出现错误：(gnome-ssh-askpass:2878): Gtk-WARNING **: cannot open display:

    echo 'unset SSH_ASKPASS' >> ~/.bashrc && source ~/.bashrc

### 2)删除子模块, 分四步:

  第一步: 根据路径删除子模块的记录

     git rm --cached [path]

  第二步: 编辑“.gitmodules”文件，将子模块的相关配置节点删除掉

  第三步: 编辑“ .git/config”文件，将子模块的相关配置节点删除掉

  第四步: 手动删除子模块残留的目录


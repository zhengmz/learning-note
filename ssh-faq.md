SSH 使用笔记
====

一、信任登录
----

### 1.1 在本机执行命令：

    ssh-keygen

生成两个文件，如id_rsa和id_rsa.pub，其中, id_rsa是私钥，id_rsa.pub是公钥

### 1.2 拷贝公钥id_rsa.pub中的内容到远程机器

如远程机器为www.example.com，准备登录使用的用户是user1, 那么拷贝到/home/user1/.ssh/authorized_keys文件中.

### 1.3 完成信任连接

可以应用到以下场景：

1. 远程信任登录 如：


    ssh user1@www.example.com


2. 直接执行远程命令


    ssh user1@www.example.com "ls"


3. 还可以实现与ssh相关的其他场景如git


    git clone ssh://user1@www.example.com/opt/git/test.git


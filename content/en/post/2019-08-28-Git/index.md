---
authors:
- Fiona
categories: [Git]
date: "2019-08-28T00:00:00Z"
draft: false
featured: true
image:
  caption: ""
  focal_point: ""
projects: []
subtitle: Learn how to use github
summary: Learn how to use github
tags: []
title: Github Summary
---


{{% toc %}}


介绍Github的常用命令和用法。

## 1.常用git命令

### 1.1 远程库与本地库建立一对一关联

首先要把远程库与本地库一对一建立关联（如果已经建立，忽略这一步）。

假设你准备把本地文件夹（名称为demo）与远程仓库（名称为Repo）建立关联。

#### 通过https关联

首先进入demo文件夹，右键，Git Bush Here，输入

```
ggit init // 把这个目录变成Git可以管理的仓库
git remote add origin git@github.com:用户名/Repo.git // 关联远程仓库
```

#### 通过ssh关联（一台电脑只需关联一次）

把公钥`~/.ssh/id_rsa.pub`里的内容复制到远程库的 `ssh and gpg keys`, 具体操作见 "为github账号配置ssh key" 一节。这样本地库就与该账号关联了。

### 1.2 把本地仓库推送到远程库

Git Bash：

```
git add README.md // 把文件README.md 添加到本地仓库
git add . // 把整个目录所有文件全部添加到仓科 
git commit -m "first commit" // 把文件提交到仓库
git push -u origin master // 把本地库的所有内容推送到远程库上　　
```
Git GUI：

在项目的根目录，按右键，选择`Git GUI Here`，按下图步骤，把所有本地有变动的文件推送到远程仓库。

![](./deploy.png)

### 1.3 把远程仓库文件提取到本地仓库

```
git fetch origin master // 从远程仓库抓取文件（并没有放进本地仓库）
git merge origin/master // 把从远程仓库抓取的文件内容合并到本地

或者：  
git pull origin master  //将远程库中的更改合并到当前分支中，相当于
                        //pull = fetch + merge（后者更安全）
```
### 1.4 把远程仓库克隆到本地


把远程仓库整个提取到本地一个文件夹（可以不是仓库），可以用`clone`：

```
git clone git@github.com:用户名/Repo.git <本地仓库名>
```

## 2. Git多账号配置SSH Key

假设原有`用户名1`和`邮箱1`，现在想在同一部电脑上增加第二个`用户名2`和`邮箱2`。

### 2.1 检查电脑上现有的ssh key

在Git BASH 上输入`$ cd ~/.ssh` 或直接 打开 C:\用户\你的电脑名\.ssh\，查看现有key文件:

`$ ls`

一般会有如下文件：id_rsa, id_rsa.pub，known_hosts。如果显示“No such file or directory”，表示还没有生成任何ssh key，可按照下节方法生成 id_rsa，id_rsa.pub，known_hosts（一路回车，不要重命名）。

### 2.2 配置

先将第一个设置为**全局配置**：
```
$ git config --global user.name "用户名1"
$ git config --global user.email "邮箱1"
```
如果在安装Git时已经绑定这个邮箱，这一步可以忽略。

第二个设置为**局部配置**。进入到项目根目录，执行：（其他局部设置都同样操作）

```
git config user.name "用户名2"
git config user.email "邮箱2"
```
### 2.3 生成第二个SSH Key

输入下面的代码，就可以生成新的key文件。不要一路回车，对文件进行重命名。

```
$ ssh-keygen -t rsa -C "邮箱2"
```

会显示：

```
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):
```

输入文件名`id_rsa_2`。 然后系统会要你输入加密串（Passphrase）：

`Enter passphrase (empty for no passphrase):`<输入加密串> （一般不设密码）  
`Enter same passphrase again:`<再次输入加密串>  
`ssh key success`

这就生成了用户2的ssh key。

### 2.4 为 github 账号配置 ssh key

切换到 github网页，展开个人头像的小三角，点击settings，然后打开 SSH and GPG keys 菜单， 点击 New SSH key 新增密钥

![](./sshkey.png)

标题最好和仓库名一致, 便于区分。将 id_rsa.pub 文件中 key 粘贴到此，最后 Add SSH key 生成密钥

![](./sshadd.png)

用户1的SSH keys配置完成。同样操作用户2.

### 2.5 配置 config

首先在目录 `C:\用户\admin（电脑名）\.ssh\`创建config文件，内容为：

```
##Default account1 (email1)
Host git@github.com
HostName https://github.com (为了与单个global用户名一致)
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa
     
##second account2（email2）
Host Name2
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_2
```

测试一下。输入下面的命令，看看设置git@github.com和Name2是否成功：

$ ssh -T git@github.com

如果出现如下信息，说明设置成功：

Hi <em>username</em>! You've successfully authenticated, but GitHub does not provide shell access.

## 3. 在RStudio中应用Git

项目托管和版本控制一般用Git GUI或Git Bash。然而Rstudio 已经聚合了Git跟踪项目，可以很方便地随时进行版本控制。

假设你已经安装了Git并且准备好了一个Github仓库。

### 3.1 RStudio全局设置

Go to `Global Options` (from the `Tools` menu)，Click `Git/SVN`, Click `Enable version control interface for RStudio projects`。If necessary, enter the path for your Git or SVN executable where provided. You can also create or add your RSA key for SSH if necessary.

![](./RstudioGit1.png)

### 3.2 建立项目与远程仓库的关联  

复制仓库SSH地址：登进Github仓库，点开`Clone or Download`，右边拷贝SSH clone URL(注意不是网址！)。

![](./gitclone.png)

打开RStudio，File -> New Project （如果已经存在与Github仓库相同的Project，直接打开`.Proj`文件）,
打开-> Version Control -> Git -> Paste the SSH clone URL。

注意New Project的名字和Github仓库名字要一致。

### 3.3 项目更新推送到远程托管

创建成功后，Rstudio自动将工作目录转到新创建的项目，并且在右上操作框出现Git按键，通过Rstudio 或者Github Desktop 来实现Github 托管项目都可以。

![](./RstudioGit.png)


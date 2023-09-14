---
layout: post
title: Linux下搭建SVN服务器并实现提交自动更新
categories: linux
description: Linux下搭建SVN服务器并实现提交自动更新
keywords: linux, svn
---



**Linux下搭建SVN服务器并实现提交自动更新**

------



### 通过yum命令安装svnserve，命令如下

```shell
yum -y install subversion
```

> 若需要查看svn安装位置，可以使用：
>
> `rpm -ql subversion`
>
> 检测是否安装成功：
>
> `svnserve --version`



### 创建版本库目录

> subversion默认以 `/var/svn` 作为数据根目录，可以通过 `/etc/sysconfig/svnserve` 修改这个默认位置
>
> `vim /etc/sysconfig/svnserve`
>
> 可修改文件内容
>
> `OPTIONS="-r /var/svn"`

```shell
mkdir /data/svn
```

创建svn版本库

```shell
svnadmin create /data/svn/carpedx
```

创建成功后，进入carpedx目录下

```
cd /data/svn/carpedx
```

进入目录可以看见如下信息：

<img src="/images/posts/linux/linux_svnserve_step1.jpg"  />



### 配置修改

进入创建好的版本库目录下

```shell
cd /data/svn/carpedx/conf
```

> authz：负责账号权限管理，控制账号是否读写权限
>
> passwd：负责账号和密码的用户名单管理
>
> svserve.conf：svn服务器配置文件

1. 编辑 `authz` 文件，最下方增加：

   ```shell
   admin = carpedx
   
   [/]
   @admin = rw
   ```

   > [/] 表示根目录，即 `/var/svnrepos`
   >
   > `@admin=rw` 和 `admin = carpedx` 表示给carpedx用户赋予读写权限

   <img src="/images/posts/linux/linux_svnserve_step2.jpg" />

2. 编辑 `passwd` 文件，最下方增加：

   ```shell
   carpedx = 123456
   ```

   <img src="/images/posts/linux/linux_svnserve_step3.jpg" />

3. 编辑 `svnserve.conf`，[general] 最下方增加：

   ```shell
   anon-access = none
   auth-access = write
   password-db = passwd
   authz-db = authz
   ```

   <img src="/images/posts/linux/linux_svnserve_step4.jpg" />



### 另外：需要检查防火墙

防火墙开启的情况下需要配置端口，否则无法连接svn

查看 firewall 服务状态：

```shell
systemctl status firewalld
```

> 如果输出显示 "Active: active (running)"，则表示防火墙已开启。如果显示 "Active: inactive (dead)"，则表示防火墙已停止。

查看 firewall 状态：

```shell
firewall-cmd --state
```

> 如果输出显示 "running"，则表示防火墙已开启。如果显示 "not running"，则表示防火墙已停止。

开启、重启、关闭 firewalld.service服务：

```shell
# 开启
sudo systemctl start firewalld  
# 重启
sudo systemctl restart firewalld  
# 关闭
sudo systemctl stop firewalld
```

> 这些命令可以分别用于开启、重启或关闭 firewalld.service服务。使用这些命令后，您需要再次检查 firewall 服务状态以确认服务是否已经启动或停止。

开放防火墙端口

```shell
firewall -cmd --permanent --add-port=3690/tcp
```

重启防火墙

```shell
systemctl restart firewalld
```



### 启动svn服务器

```shell
svnserve -d -r /data/svn
```

另外：

```shell
# 停止
killall svnserve

# 再运行
svnserve -d -r /data/svn

# 查看是否启动成功
ps -ef | grep 'svnserve'
netstat -ln | grep 3690
```



### 创建文件夹并检出

在svn仓库的 `hooks` 目录下，复制 `post-commit.tmpl` 为 `post-commit` ，并写入配置文件

```shell
cd /data/svn/carpedx/hooks
```

```shell
cp post-commit.tmpl post-commit
```

给 `post-commit` 添加可执行权限

```shell
chmod a+x post-commit
```

编辑 `post-commit`

```shell
#!/bin/sh

export LANG=en_US.UTF-8

nohup ~/script/svnhook.sh &>/dev/null &
```

<img src="/images/posts/linux/linux_svnserve_step5.jpg" />

**设置 `svnhook.sh`**

进入 script 目录 ，创建 `svnhook.sh`，和 `svn.updateweb.sh`，并添加可执行权限

```shell
cd ~/script/
```

```
touch svnhook.sh
touch svn.updateweb.sh
```

```shell
chmod a+x svnhook.sh
chmod a+x svn.updateweb.sh
```

`svnhook.sh` 内容如下：

```
# svn file sync to web path
~/script/svn.updateweb.sh
```

`svn.updateweb.sh` 内容如下：

```
# release

svn update /data/nfs/www/carpedx --force --username carpedx --password 123456 --no-auth-cache
```

从 `/data/nfs/www` 目录下拉取 svn carpedx 仓库：

```shell
svn checkout svn://127.0.0.1/carpedx
```



后续所有对 svn carpedx 仓库上的提交都会同步到 /data/nfs/www/carpedx 目录

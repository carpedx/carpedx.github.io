---
layout: post
title: PHP使用Spudervisor管理进程
categories: php
description: PHP使用Spudervisor管理进程
keywords: php, spudervisor
---



**Supervisor 是用 Python 开发的一个在 Linux 系统下的进程管理工具，可以方便的监听，启动，停止一个或多个进程。当一个进程被意外杀死后，Supervisor 监听到后，会自动重新拉起进程。**

------



### 安装  Supervisor 

```shell
yum -y install supervisor
```

安装好后，会生成三个执行命令，`echo_supervisord_conf`，`supervisorctl`，`supervisord`



### Supervisor 的配置文件

supervisor 的默认配置文件在 `/etc/supervisord.conf` 下，如果没有可以通过如下命令生成

```shell
echo_supervisord_conf > /etc/supervisord.conf
```

常用的配置项如下：

```shell
[unix_http_server]
file=/var/run/supervisor/supervisor.sock   ; unix socket文件，supervisorctl会使用
;chmod=0700                 ; socket文件权限
;chown=nobody:nogroup       ; socket文件所属用户和用户组

;[inet_http_server]         ; web管理界面，默认不开启
;port=127.0.0.1:9001        ; 管理界面的IP和端口
;username=user              ; 登陆管理界面的用户名
;password=123               ; 登陆管理界面的密码

[supervisord]
logfile=/var/log/supervisor/supervisord.log  ; 日志文件
logfile_maxbytes=50MB       ; 日志文件大小，为0表示不限制
logfile_backups=10          ; 日志文件备份数量，为0表示不备份
loglevel=info               ; 日志级别，也可设置为 debug,warn,trace
pidfile=/var/run/supervisord.pid ; PID文件路径
nodaemon=false              ; 是否前台启动，为false表示守护进程方式
minfds=1024                 ; 打开文件描述符的最小值
minprocs=200                ; 创建进程数的最小值

[supervisorctl]
serverurl=unix:///var/run/supervisor/supervisor.sock ; 通过 unix sokCET 连接supervisord
;serverurl=http://127.0.0.1:9001 ; 通过http方式连接supervisord

[include]
files = supervisord.d/*.ini;  包含其他配置文件，可以是.conf或.ini
```


### 配置一个PHP脚本进程

在 `/etc/supervisord.d` 目录下创建一个 `testtest.ini` 文件

```shell
;testtest表示程序名称
[program:testtest]
;需要执行的命令
command=php testtest.php
;命令执行的目录
directory=/home/wwwroot/www.xxx.com
;环境变量
environment=PATH="/usr/local/php/bin/"
;哪个用户运行
user=root
;是否自启动
autostart=true
;是否自动重启
autorestart=true
;自动重启时间间隔，单位秒
startsecs=3
;错误日志文件
stderr_logfile=/tmp/testtest.err.log
;输出日志文件
stdout_logfile=/tmp/testtest.out.log
```

testtest.php 代码：

```she
<?PHP
 
$i = 0;
while(true) {
    $i++;
    echo $i,PHP_EOL;
    sleep(1);
}
```



### 进程管理

管理进程，需要我们启动 `supervisor` 服务，这里我们配置 `systemctl`，开机自动启动 `supervisor`。
创建 `/usr/lib/systemd/system/supervisord.service` 文件，配置如下：

```shell
[Unit]
Description=Process Monitoring and Control Daemon
After=rc-local.service nss-user-lookup.target

[Service]
Type=forking
# -c 指定配置文件
ExecStart=/usr/bin/supervisord -c /etc/supervisord.conf 
ExecStop=/usr/bin/supervisorctl $OPTIONS shutdown
ExecReload=/usr/bin/supervisorctl $OPTIONS reload
Killmode=process
Restart=on-failure
RestartSec=42s

[Install]
WantedBy=multi-user.target
```


### 命令

#### 启动配置

```shell
systemctl enable supervisord.service
```

#### 启动 supervisord 服务

```shell
systemctl start supervisord.service
```

成功后，就可以通过 `supervisorctl` 交互命令管理进程脚本了。

#### 读取有更新的配置文件

```shell
supervisorctl reread
```

#### 更新配置文件修改过的程序

```shell
supervisorctl update
```

#### 如果修改过 `/etc/supervisord.conf` 请使用如下命令

```shell
supervisorctl reload
```

#### 启动，停止，重启

```shell
supervisorctl start 程序名
supervisorctl stop 程序名
supervisorctl restart 程序名
```



### supervisor图形化管理界面

需要开启 `/etc/supervisord.conf` 文件中的 `[inet_http_server]`

```shell
[inet_http_server]
port=0.0.0.0:9001
username=admin
password=123456
```

设置完后，要开放 9001 端口，并重启 `supervisor`

```shell
firewall-cmd --zone=public --add-port=9001/tcp --peRMANent
firewall-cmd --reload
```

重启 `supervisor`

```shell
supervisorctl reload
```




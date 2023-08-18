---
layout: fragment
title: 解决yarn网络连接异常报错问题
tags: [yarn]
description: 解决yarn网络连接异常报错问题
keywords: yarn
---



**解决 `yarn` 网络连接异常，一直重试报错 `tunneling socket could not be established...` 导致包一直下载不下来 **

> 该异常就算关闭了代理也没用，还是会报错

------



## 解决方法

1）打开代理，查看自己的代理地址（比如此处我的代理是127.0.0.1:9999）

<img src="/images/fragments/yarn/network-connection-retrying_step1.jpg" />

2）修改 `yarn` 和 `npm` 代理源

npm：

```shell
npm config set proxy http://127.0.0.1:9999
npm config set https-proxy http://127.0.0.1:9999
```

```shell
yarn config set proxy http://127.0.0.1:9999
yarn config set https-proxy http://127.0.0.1:9999
```

3）重新打开 cmd 执行命令即可

4）如果以上还是解决不了可尝试切换下载源

淘宝源：

```shell
npm config set registry http://registry.npm.taobao.org/
yarn config set registry http://registry.npm.taobao.org/
```

官方源：

```shell
npm config set registry https://registry.npmjs.org/
yarn config set registry https://registry.npmjs.org/
```



## 以下是一些其他命令，如果最后还是不行用来恢复初始设置

**删除代理**

npm删除代理

```shell
npm config delete proxy
npm config delete https-proxy
```

yarn删除代理

```shell
yarn config delete proxy
yarn config delete https-proxy
```

------



**卸载重装 `yarn` **

查看npm全局已安装

```shell
npm ls -g
```

卸载yarn命令

```shell
npm uninstall -g yarn
```

安装yarn命令

```shell
npm install -g yarn
```

------



非 Windows 系统下如果 `npm uninstall -g yarn` 之后，`yarn` 还是可用的，可以尝试手动删除

```shell
rm -rf /usr/local/lib/node_modules/yarn
```

```shell
rm -rf /usr/local/bin/yarn yarnpkg
```


---
layout: fragment
title: PHP实现RSA加密解密
tags: [php]
description: PHP实现RSA加密解密
keywords: php
---



#### 生成密钥对

[密钥生成对](https://www.ssleye.com/ssltool/pass_double.html)



#### 代码加密

```php
<?php

namespace app\commons\lib;

/**
 * RSA加密解密
 */
class RsaSign
{

    // 公钥和私钥的一些配置
    private $options = [
        'private_key' => '-----BEGIN PRIVATE KEY-----
MIICdwIBADANBgkqhkiG9w0BAQEFAASCAmEwggJdAgEAAoGBANBFVp2xtv7Ix7Oc
7OP5uEVo2Zc0mUMf3rcUtB8tf9UTNER1rRG01RMH/OT24b08qePsmpj7PqrQ9hHR
sQaBNSwCO847idh1RsXiWwlSdzL87UjD1zmQu97YqoVqgOlSmZeHmxsDicX+a0NK
Ly1rWU08HaJskweuBgfIRjfyPuwVAgMBAAECgYEAvJ2PC9FeQO/kq616fBwz3ehO
5rkj875xHK0LUxEJrBZwR2+rBVYXgdzCEznixlaiJWCpwdIHtvr3Xrvg7lslosrP
xdi+UwnJedtwZ+OQzVevehZ9IpYbQUpMtD5ps738pxUPL7DOJvLyDXaY7+yKbswJ
ZpoEohaW1+I/12pjzgECQQDyEPUDrcYDG622IGdSOd3a3UfgK10MoQ0dZLON/FEg
rNPcfDj7rpaS/VqebTabDBOB+Q1ecBusH44CcMOsJU5RAkEA3EJg9msVRmUv0ptx
OhGTNQ31wK+DrwFJlNd50rojdmrlehhXP2K50Yc8CNyb743Oa4mR5oJziHg8UBm8
AI58hQJBAKsCVcXajwgFFsySyctqPBRxvWRd3iec42H/kWXeegXmcunSDIOpjJCd
UdaYezo/5swsgUhVVQddZ3rL5UCJPnECQFa5+E3u2iCuxQbLV+kCdJEmXwaw1Uv6
WMu3kGtOCZqd+p95qBj4Oaxr8FKY8vl/P7EOKRm3ceBhX3Y2u9UIRbUCQHO+AwAF
0M8yNin++2x2oQ9x4+ExhoK7rEYEd/b9n0nZvobk0jVU0HiEb4DEUtKZxWEwrasb
D8cpEPVhlXcpECU=
-----END PRIVATE KEY-----',
        'public_key' => '-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDQRVadsbb+yMeznOzj+bhFaNmX
NJlDH963FLQfLX/VEzREda0RtNUTB/zk9uG9PKnj7JqY+z6q0PYR0bEGgTUsAjvO
O4nYdUbF4lsJUncy/O1Iw9c5kLve2KqFaoDpUpmXh5sbA4nF/mtDSi8ta1lNPB2i
bJMHrgYHyEY38j7sFQIDAQAB
-----END PUBLIC KEY-----',
    ];

    //保存例实例在此属性中
    private static $_instance;

    /**
     * 私有构造函数，防止外界实例化对象
     */
    private function __construct()
    {
    }

    /**
     * 私有克隆函数，防止外办克隆对象
     */
    private function __clone()
    {
    }

    /**
     * 静态方法，单例统一访问入口
     */
    public static function getInstance()
    {
        if (is_null(self::$_instance) || isset (self::$_instance)) {
            self::$_instance = new self ();
        }
        return self::$_instance;
    }

    /**
     * 获取私钥内容
     */
    private function getPrivateKey(){

        if (empty($this->options['private_key'])){
            throw new \Exception('私钥为空');
        }
        extension_loaded('openssl') or die('php需要openssl扩展支持');
        return $this->options['private_key'];
    }

    /**
     * 获取公钥内容
     */
    public  function getPublicKey(){
        if (empty($this->options['public_key'])){
            throw new \Exception('请配置公钥');
        }
        extension_loaded('openssl') or die('php需要openssl扩展支持');
        return $this->options['public_key'];
    }

    /**
     * 公钥加密
     */
    public function createKey($data)
    {
        $encrypted = '';
        $public_key = openssl_pkey_get_public(self::getPublicKey());
        try {
            openssl_public_encrypt($data, $encrypted, $public_key);
            return base64_encode($encrypted);
        } catch (\Exception $exception) {
            return false;
        }
    }

    /**
     * 用私钥解密公钥加密内容
     */
    public function decodeKey($data)
    {
        $decrypted = '';
        // 如果生成密钥对时设置了密码则第二个参数传入密码
        $private_key = openssl_pkey_get_private(self::getPrivateKey());
        try {
            openssl_private_decrypt(base64_decode($data), $decrypted, $private_key);
            return $decrypted;
        } catch (\Exception $exception) {
            return false;
        }
    }
}
```

调用方式：

```php
$params = 'helloWord!';
$sign = RsaSign::getInstance()->createKey($params);	// 加密
$private_key =  RsaSign::getInstance()->decodeKey($sign);	// 解密
```



#### 文件加密

1）使用[php-beast](https://github.com/liexusong/php-beast)加密文件

2）编译安装如下

```php
$ wget https://github.com/liexusong/php-beast/archive/master.zip
$ unzip master.zip
$ cd php-beast-master
$ phpize
$ ./configure
$ sudo make && make install
编译好之后修改php.ini配置文件, 加入配置项: extension=beast.so, 重启php-fpm
```

3）配置项php.ini

```php
 beast.cache_size = size
 beast.log_file = "path_to_log"
 beast.log_user = "user"
 beast.log_level = "debug"
 beast.enable = On
```

4）加密方案

先修改`tools`目录下的`configure.ini`文件

```php
; source path
src_path = "/lib_src/"

; destination path
dst_path = "/lib_dst/"

; expire time
expire = ""

; encrypt type
encrypt_type = "DES"
```

`src_path`是可以要加密项目的路径，`dst_path`是保存加密后项目的路径，`expire`是设置项目可使用的时间（`expire`的格式是：`YYYY-mm-dd HH:ii:ss`）。`encrypt_type`是加密的方式，选择项有：DES、AES、BASE64。修改完`configure.ini`文件后就可以了使用命令`php encode_files.php`启动加密项目。

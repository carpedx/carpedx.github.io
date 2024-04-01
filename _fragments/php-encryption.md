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



#### 文件加密方式一（screw plus）

1）首先克隆一份代码到服务器

```php
git clone https://git.oschina.net/splot/php-screw-plus.git
```

2）进入项目目录，`phpize` 是官方提供的可执行文件用于动态生成扩展开发环境，一般在php的bin目录下可以找到。lnmp的 `phpize` 在`/usr/local/php/bin/phpize`

```php
cd php-screw-plus
phpize
```

3）配置 `configure` ， `php-config` 一般也在php的bin目录下，写绝对路径就可以了

```php
./configure --with-php-config=/usr/local/php/bin/php-config
```

4）编译

> 编译之前，我们可以修改加密的key，打开 `php_screw_plus.h` 可以看到开头就是 #define CAKEY

执行 `make` 命令，如果最后显示Build complete。说明编译成功。扩展在modules里面，如果报错请根据提示进行修复，然后make clean之后重新编译。

```php
sudo make && make install
```

5）上面我们编译的是解密程序，而加密程序也需要我们手动编译一下，进入tools目录执行make命令即可

```php
cd tools/
sudo make
```

6）然后需要把扩展的路径加入到php.ini中

```php
vi php/etc/php.ini
    
[screw]
extension=/opt/php-screw-plus/modules/php_screw_plus.so
```

7）使用

```php
cd /opt/php-screw-plus/tools/
    
加密
./screw /home/web/test.php
    
解密
./screw /home/web/test.php -d
```

>PS：如果遇到找不到类的异常  Class 'app\\commons\\lib\\RsaSign' not found 可以考虑换下面 beast 这种加密方式



#### 文件加密方式二（php beast）

1）使用 [php-beast](https://github.com/liexusong/php-beast) 加密文件

2）编译安装如下

```php
wget https://github.com/liexusong/php-beast/archive/master.zip
unzip master.zip
cd php-beast-master
phpize
./configure --with-php-config=/usr/local/php/bin/php-config
sudo make && make install
```

3）配置项 `php.ini`

```php
[beast]
extension = beast.so
```

4）加密方案（单个文件）

```php
cd tools
php encode_file.php --encrypt DES --oldfile /www/test/Login.php --newfile /www/test/Login2.php
```

5）加密方案（多个文件）

先修改`tools`目录下的`configure.ini`文件

```php
; 要加密项目的路径
; source path
src_path = "/lib_src/"

; 保存加密后项目的路径
; destination path
dst_path = "/lib_dst/"

; 设置项目可使用的时间
; expire time
expire = ""

; 加密的方式
; encrypt type
encrypt_type = "DES"
```

启动加密项目：

```php
cd tools
php encode_files.php
```



#### 文件加密方式三（tonyenc）

类似上面两种



#### 在线加密

https://www.toolfk.com/tools/convert-php.html

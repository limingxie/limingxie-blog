---
author: "li_mingxie"
title: "【FISCO笔记】账户创建和使用"
date: 2024-02-24T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "fisco",
    "bcos",
]
categories: [
    "fisco",
    "account",
]
---

FISCO BCOS也是基于公私钥方式管理用户和账户地址。  
为了方便操作，官方直接提供了管理用户的脚本，  
国密生成账户脚本get_gm_account.sh与非国密get_account.sh。  <!--more-->  

## 1.获取脚本

下载地址：https://github.com/FISCO-BCOS/console/tree/master/tools

非国密

```bash
curl -#LO https://raw.githubusercontent.com/FISCO-BCOS/console/master/tools/get_account.sh && chmod u+x get_account.sh && bash get_account.sh -h
```

国密

```bash
curl -#LO https://raw.githubusercontent.com/FISCO-BCOS/console/master/tools/get_gm_account.sh && chmod u+x get_gm_account.sh && bash get_gm_account.sh -h
```

```bash
Usage: get_account.sh
    default       generate account and store private key in PEM format file
    -p            generate account and store private key in PKCS12 format file
    -k [FILE]     calculate address of PEM format [FILE]
    -P [FILE]     calculate address of PKCS12 format [FILE]
    -a            force script to consider the platform is aarch64
    -h Help
```

## 2.生成密钥

生成私钥与地址

```bash
$ bash get_account.sh
[INFO] Account Address   : 0x3015d7eb7cfaf0ca34d507a62bbfdab17a68832b
[INFO] Private Key (pem) : accounts/0x3015d7eb7cfaf0ca34d507a62bbfdab17a68832b.pem
[INFO] Public  Key (pem) : accounts/0x3015d7eb7cfaf0ca34d507a62bbfdab17a68832b.pem.pub
```

生成的秘钥的默认格式为PEM格式，添加 -p 参数可以生成PKCS12格式的密钥。

```bash
$ bash get_account.sh -p
[INFO] Note: the entered password cannot contain Chinese characters!
Enter Export Password:
Verifying - Enter Export Password:
[INFO] Account Address   : 0xc65858692fce953b44187999dbb3d5de91c27bb4
[INFO] Private Key (p12) : accounts/0xc65858692fce953b44187999dbb3d5de91c27bb4.p12
[INFO] Public  Key (pem) : accounts/0xc65858692fce953b44187999dbb3d5de91c27bb4.pem.pub
```

## 3.手动创建账号和地址计算

FISCO BCOS的账户地址由ECDSA公钥计算，对ECDSA公钥的16进制表示计算keccak-256sum哈希，取计算结果的后20字节的16进制表示作为账户地址，每个字节需要两个16进制数表示，所以账户地址长度为40。  
FISCO BCOS的账户地址与以太坊兼容。  

生成ECDSA私钥

```bash
$ openssl ecparam -name secp256k1 -genkey -noout -out ecprivkey.pem

$ cat ecprivkey.pem
-----BEGIN EC PRIVATE KEY-----
MHQCAQEEIInB6hH8PF37ofPoHXHGTN5ONXy7I0WDAT87/Sfye/3OoAcGBSuBBAAK
oUQDQgAES3GjvnJxbrZvkCSzxJE8I7xCkbJYKYtevIEOGKSCxqP3/hHC4CH0rMYS
zXQK8Jls07jSZdmcX6pkW7dHqiHd2w==
-----END EC PRIVATE KEY-----
```

根据私钥计算公钥

```bash
$ openssl ec -in ecprivkey.pem -text -noout 2>/dev/null| sed -n '7,11p' | tr -d ": \n" | awk '{print substr($0,3);}'
4b71a3be72716eb66f9024b3c4913c23bc4291b258298b5ebc810e18a482c6a3f7fe11c2e021f4acc612cd740af0996cd3b8d265d99c5faa645bb747aa21dddb
```

使用keccak-256sum工具计算账户地址
<https://github.com/vkobel/ethereum-generate-wallet/tree/master/lib>

```bash
$ openssl ec -in ecprivkey.pem -text -noout 2>/dev/null| sed -n '7,11p' | tr -d ": \n" | awk '{print substr($0,3);}' | ./keccak-256sum -x -l | tr -d ' -' | tail -c 41
fa7dbe2e84996351b77a7c5ab26db08666064705
```

## 4.控制台加载账户

```bash
$ bash start.sh
$ bash start.sh group0
$ bash start.sh group0 -pem pemName
$ bash start.sh group0 -p12 p12Name

$ bash start.sh group0 -pem accounts/0x3015d7eb7cfaf0ca34d507a62bbfdab17a68832b.pem
$ bash start.sh group0 -p12 accounts/0xc65858692fce953b44187999dbb3d5de91c27bb4.p12
Enter Export Password:
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`

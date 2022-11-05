---
author: "li_mingxie"
title: "【算法笔记】sign_数字签名的简实现"
date: 2022-11-02T23:28:49+08:00
tags: [
    "密码学",
    "sign",
]
categories: [
    "algorithm",
]
---

这篇文章简单的整理了`数字签名的简实现`。  <!--more-->  

## 1.数字签名的概念

我们先看看[维基百科](https://zh.m.wikipedia.org/zh-hans/%E6%95%B8%E4%BD%8D%E7%B0%BD%E7%AB%A0)的解释。  

> 数位签章（英语：Digital Signature，又称公钥数位签章）是一种功能类似写在纸上的普通签名、  
但是使用了公钥加密领域的技术，以用于鉴别数字讯息的方法。  
一套数字签名通常会定义两种互补的运算，一个用于签名，另一个用于验证。法律用语中的电子签章与数位签章代表之意义并不相同。  
电子签章指的是依附于电子文件并与其相关连，用以辨识及确认电子文件签署人身分、资格及电子文件真伪者；  
数位签章则是以数学演算法或其他方式运算对其加密而形成的电子签章。意即并非所有的电子签章都是数位签章。  

## 2.签名需要满足的条件

1. 签名不可伪造
2. 签名不可抵赖
3. 签名需可信
4. 签名不可复制
5. 签名消息不可篡改

## 3.简单实现

实际用的时候，使用bouncycastle库做签名验证。  
下面的代码只是一个简单的模拟。  

```java
package org.example;

import org.junit.Test;

import java.security.KeyFactory;
import java.security.PrivateKey;
import java.security.PublicKey;
import java.security.spec.X509EncodedKeySpec;
import java.util.Base64;

import java.security.spec.PKCS8EncodedKeySpec;

/**
 * @author : 李明燮
 * @since : 2022/11/2
 * create at : 2022/11/1 11:09 PM
 */
public class SignTest {

    //模拟数字签名过程
    //模拟发送方
    public static String makeSigMsg(String strPrivateKey, String data){
        //私钥说明书
        PKCS8EncodedKeySpec pkcs8EncodedKeySpec = new PKCS8EncodedKeySpec(Base64.getDecoder().decode(strPrivateKey.getBytes()));

        try{
            KeyFactory keyFactory = KeyFactory.getInstance("RSA");
            PrivateKey privateKey = keyFactory.generatePrivate(pkcs8EncodedKeySpec);

            //对data进行hash算法后，执行私钥加密
            data = HashUtil.sha(data.getBytes());
            String encData = RsaUtil.privKeyEncrytData(data, privateKey);

            return encData;
        }catch (Exception e){
            e.printStackTrace();
        }
        return null;
    }

    //模拟接收方
    public static boolean verifSigMsg(String strPubKey, String data, String encMsg){
        X509EncodedKeySpec x509EncodedKeySpec = new X509EncodedKeySpec(Base64.getDecoder().decode(strPubKey.getBytes()));

        try{
            KeyFactory keyFactory = KeyFactory.getInstance("RSA");
            PublicKey publicKey = keyFactory.generatePublic(x509EncodedKeySpec);

            String decData = RsaUtil.pubKeyDecrytData(encMsg,publicKey);

            if(decData.equals(HashUtil.sha(data.getBytes()))){
                System.out.println("数字签名验证通过");
                return true;
            } else {
                System.out.println("数字签名验证失败");
                return false;
            }
        }catch (Exception e){
            e.printStackTrace();
        }
        return false;
    }

    @Test
    public void test(){
        String strPubKey = "MFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBAKgi54xoLp9XLdxKrdTK18kkFThuX0PTeMwu+BpOfY8dUUj+/iF4eaANACvlw1NruUBJad66DjnzHnyMZ6hfZJ0CAwEAAQ==";
        String strPriKey = "MIIBVAIBADANBgkqhkiG9w0BAQEFAASCAT4wggE6AgEAAkEAqCLnjGgun1ct3Eqt1MrXySQVOG5fQ9N4zC74Gk59jx1RSP7+IXh5oA0AK+XDU2u5QElp3roOOfMefIxnqF9knQIDAQABAkA7sxkQS7WyMhxAieurENMJwlwei2IcDSxpI4xImvIOLD6RW05NyOdQViNJUCSCIw7hUjL4MjfiXUid8iP9dOlpAiEA/G8kpjGmSuaczv4sRy4RUp7b/hYKIo7PBs94QXvDVPMCIQCqguwPLyzX0pI790ZCaHc8LvVIpDdB7yj8/tT6/NUELwIgHyc4Rqpp82RyhbkojcBJswCX9cuShKFLO3PvS2WSvbECICJbMN86XSm7ZvWoNELrM6MvvwJ8/BlP7A1V92n/CZjHAiEAgVVKXN98r/n/Rto9Z1RiH2Tdb37F/M65H0gBUXWmbpE=";

        String data = "做签名测试了~~";

        String encMsg = makeSigMsg(strPriKey, data);
        System.out.println(encMsg);

        boolean flag = verifSigMsg(strPubKey,data,encMsg);
        System.out.println(flag);
    }
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`

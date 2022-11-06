---
author: "li_mingxie"
title: "【算法笔记】RSA_算法简单实践"
date: 2022-11-01T23:28:49+08:00
tags: [
    "密码学",
    "rsa",
]
categories: [
    "algorithm",
]
---

这篇文章简单的整理了`RSA算法的简单实践`。  <!--more-->  

## 1.RSA加密算法

我们先看看[维基百科](https://zh.wikipedia.org/wiki/RSA%E5%8A%A0%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95)的解释

RSA加密算法是一种非对称加密算法，在公开密钥加密和电子商业中被广泛使用。

## 2.密钥对生成步骤

RSA算法是利用了两个大素数相乘容易，公式分解困难的原理。  
因此公开乘积公开作为加密密钥。

### 2.1 秘钥对生成步骤

1. 随机选择两个不想的的质数p和q
2. 计算p和q的乘积N
3. 计算p-1和q-1的乘积φ(N)
4. 随机选个整数e，e与m要互质，且0 < e < φ(N)
5. 计算e的模范元素d(使得ed-1是(p-1)*(q-1)的倍数)
6. 公钥是(N,e) 私钥是(N,d)

### 2.2 加解密步骤

1. 假设一个明文m (0 <= m < N)
2. 对明文m加密成密文c
    a.c = m ^ e mod N
3. 对密文c解密成明文m
    a.m = c ^ d mod N

## 3.模拟RSA

```java
import org.junit.Test;

/**
 * @author : 李明燮
 * @since : 2022/11/1
 * create at : 2022/11/1 10:32 PM
 */
public class RsaUtil {
    public static void Rsatest() {
        int p = 11, q = 3;
        int N = 0, F = 0, c = 0, d = 0, e = 0, m = 0;

        N = p * q;
        F = (p - 1) * (q - 1);

        for (int i = 2; i < N; i++) {
            if ((F + 1) % i == 0) {
                e = i;
                break;
            }
        }
        d = (F + 1) / e;

        //公钥: (N,e)   私钥：(N,d)
        //公钥: (33,3)  私钥：(33,7)

        m = 8;
        //1.计算c
        c = (int) (Math.pow(m, e) % N);
        System.out.println("c = " + c);

        m = (int) (Math.pow(c, d) % N);
        System.out.println("m = " + m);
    }

    @Test
    public void test() {
        Rsatest();
    }
}
```

## 4.java实现RSA加解密

```java
package org.example;

import org.bouncycastle.jce.provider.BouncyCastleProvider;
import org.junit.Test;

import javax.crypto.Cipher;
import java.security.*;
import java.security.spec.PKCS8EncodedKeySpec;
import java.security.spec.X509EncodedKeySpec;
import java.util.Base64;

/**
 * @author : 李明燮
 * @since : 2022/11/1
 * create at : 2022/11/1 10:33 PM
 */
public class RsaUtil {
    public static void createKeyPairs() {
        Security.addProvider(new BouncyCastleProvider());
        try {
            KeyPairGenerator generator = KeyPairGenerator.getInstance("RSA");
            generator.initialize(512, new SecureRandom());
            KeyPair pair = generator.generateKeyPair();
            PrivateKey privateKey = pair.getPrivate();
            PublicKey publicKey = pair.getPublic();

            String strPublicKey = new String(Base64.getEncoder().encode(publicKey.getEncoded()));
            String strPrivateKey = new String(Base64.getEncoder().encode(privateKey.getEncoded()));
            System.out.println(strPublicKey);
            System.out.println(strPrivateKey);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static String pubKeyEncrytData(String data, PublicKey publicKey) {
        try {
            Cipher cipher = Cipher.getInstance("RSA");
            cipher.init(Cipher.ENCRYPT_MODE, publicKey);
            byte[] encData = cipher.doFinal(data.getBytes("utf-8"));
            String result = Base64.getEncoder().encodeToString(encData);
            return result;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    public static String privKeyDecrytData(String data, PrivateKey privateKey) {
        try {
            Cipher cipher = Cipher.getInstance("RSA");
            cipher.init(Cipher.DECRYPT_MODE, privateKey);
            byte[] decData = cipher.doFinal(Base64.getDecoder().decode(data));
            String result = new String(decData, "utf-8");
            return result;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    public static String privKeyEncrytData(String data, PrivateKey privateKey) {
        try {
            Cipher cipher = Cipher.getInstance("RSA");
            cipher.init(Cipher.ENCRYPT_MODE, privateKey);
            byte[] encData = cipher.doFinal(data.getBytes("utf-8"));
            String result = Base64.getEncoder().encodeToString(encData);
            return result;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    public static String pubKeyDecrytData(String data, PublicKey pubKey) {
        try {
            Cipher cipher = Cipher.getInstance("RSA");
            cipher.init(Cipher.DECRYPT_MODE, pubKey);
            byte[] decData = cipher.doFinal(Base64.getDecoder().decode(data));
            String result = new String(decData, "utf-8");
            return result;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    @Test
    public void testResult() {
        //createKeyPairs();
        String strPubKey = "MFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBAKgi54xoLp9XLdxKrdTK18kkFThuX0PTeMwu+BpOfY8dUUj+/iF4eaANACvlw1NruUBJad66DjnzHnyMZ6hfZJ0CAwEAAQ==";
        String strPriKey = "MIIBVAIBADANBgkqhkiG9w0BAQEFAASCAT4wggE6AgEAAkEAqCLnjGgun1ct3Eqt1MrXySQVOG5fQ9N4zC74Gk59jx1RSP7+IXh5oA0AK+XDU2u5QElp3roOOfMefIxnqF9knQIDAQABAkA7sxkQS7WyMhxAieurENMJwlwei2IcDSxpI4xImvIOLD6RW05NyOdQViNJUCSCIw7hUjL4MjfiXUid8iP9dOlpAiEA/G8kpjGmSuaczv4sRy4RUp7b/hYKIo7PBs94QXvDVPMCIQCqguwPLyzX0pI790ZCaHc8LvVIpDdB7yj8/tT6/NUELwIgHyc4Rqpp82RyhbkojcBJswCX9cuShKFLO3PvS2WSvbECICJbMN86XSm7ZvWoNELrM6MvvwJ8/BlP7A1V92n/CZjHAiEAgVVKXN98r/n/Rto9Z1RiH2Tdb37F/M65H0gBUXWmbpE=";

        String data = "做加密测试了~~";
        try {
            //公钥对象
            X509EncodedKeySpec x509EncodedKeySpec = new X509EncodedKeySpec(Base64.getDecoder().decode(strPubKey.getBytes()));

            //私钥对象
            PKCS8EncodedKeySpec pkcs8EncodedKeySpec = new PKCS8EncodedKeySpec(Base64.getDecoder().decode(strPriKey.getBytes()));

            KeyFactory keyFactory = KeyFactory.getInstance("RSA");
            PublicKey publicKey = keyFactory.generatePublic(x509EncodedKeySpec);
            PrivateKey privateKey = keyFactory.generatePrivate(pkcs8EncodedKeySpec);

            //1.公钥加密数据
            String encData = pubKeyEncrytData(data, publicKey);
            System.out.println("公钥加密：" + encData);

            //2.私钥解密
            String decData = privKeyDecrytData(encData, privateKey);
            System.out.println("私钥解密：" + decData);

            //3.私钥加密数据
            String encData2 = privKeyEncrytData(data, privateKey);
            System.out.println("私钥加密：" + encData2);

            //4.公钥解密
            String decData2 = pubKeyDecrytData(encData2, publicKey);
            System.out.println("公钥解密：" + decData2);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`

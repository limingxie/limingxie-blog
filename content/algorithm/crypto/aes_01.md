---
author: "li_mingxie"
title: "【算法笔记】AES_算法简单实践"
date: 2022-11-04T23:28:49+08:00
tags: [
    "密码学",
    "aes",
]
categories: [
    "algorithm",
]
---

这篇文章简单的整理了`RSA算法的简单实践`。  <!--more-->  

## 1.AES加密算法

我们先看看[维基百科](https://zh.wikipedia.org/wiki/%E9%AB%98%E7%BA%A7%E5%8A%A0%E5%AF%86%E6%A0%87%E5%87%86)的解释。

高级加密标准（Advanced Encryption Standard，缩写：AES），又称Rijndael加密法。  
现在，高级加密标准已然成为对称密钥加密中最流行的算法之一。

## 2.对称加密算法

对称密钥算法（Symmetric-key algorithm）  
又称为对称加密、私钥加密、共享密钥加密，是密码学中的一类加密算法。  
这类算法在加密和解密时使用相同的密钥，或是使用两个可以简单地相互推算的密钥。  
与公开密钥加密相比，要求双方获取相同的密钥是对称密钥加密的主要缺点之一。
常见的对称加密算法有AES、ChaCha20、3DES、Salsa20、DES、Blowfish、IDEA、RC5、RC6、Camellia。
对称加密的速度比公钥加密快很多，在很多场合都需要对称加密。

## 3.AES简单实践

#### java实现

```java
package org.example;

import javax.crypto.Cipher;
import javax.crypto.spec.SecretKeySpec;
import javax.crypto.spec.IvParameterSpec;


import org.junit.Test;
import sun.misc.BASE64Decoder;
import sun.misc.BASE64Encoder;
import java.util.Base64;

/**
 * @author : 李明燮
 * @since : 2022/11/6
 * create at : 2022/11/6 10:14 AM
 */
public class AESUtils {
    // 加密
    public static String Encrypt(String sSrc, String sKey) throws Exception {
        if (sKey == null) {
            System.out.print("Key为空null");
            return null;
        }
        // 判断Key是否为16位
        if (sKey.length() != 16) {
            System.out.print("Key长度不是16位");
            return null;
        }
        byte[] raw = sKey.getBytes("utf-8");
        SecretKeySpec skeySpec = new SecretKeySpec(raw, "AES");
        Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");//"算法/模式/补码方式"
        cipher.init(Cipher.ENCRYPT_MODE, skeySpec);
        byte[] encrypted = cipher.doFinal(sSrc.getBytes("utf-8"));
        return new BASE64Encoder().encode(encrypted);//此处使用BASE64做转码功能，同时能起到2次加密的作用。
    }

    // 解密
    public static String Decrypt(String sSrc, String sKey) throws Exception {
        try {
            // 判断Key是否正确
            if (sKey == null) {
                System.out.print("Key为空null");
                return null;
            }
            // 判断Key是否为16位
            if (sKey.length() != 16) {
                System.out.print("Key长度不是16位");
                return null;
            }
            byte[] raw = sKey.getBytes("utf-8");
            SecretKeySpec skeySpec = new SecretKeySpec(raw, "AES");
            Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
            cipher.init(Cipher.DECRYPT_MODE, skeySpec);
            byte[] encrypted1 = new BASE64Decoder().decodeBuffer(sSrc);//先用base64解密
            try {
                byte[] original = cipher.doFinal(encrypted1);
                String originalString = new String(original, "utf-8");
                return originalString;
            } catch (Exception e) {
                System.out.println(e.toString());
                return null;
            }
        } catch (Exception ex) {
            System.out.println(ex.toString());
            return null;
        }
    }

    @Test
    public void aesTest() {
        String key = "123456789abcdefg";
        String text = "aes加密测试";
        try {
            String sText = Encrypt(text, key);
            System.out.println("加密后：" + sText);
            String dText = Decrypt(sText, key);
            System.out.println("解密后：" + dText);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * @param data 明文
     * @param key  密钥，长度16
     * @param iv   偏移量，长度16
     * @return 密文
     * @author miracle.qu
     * @Description AES算法加密明文
     */
    public static String encryptAEScbc(String data, String key, String iv) throws Exception {
        try {
            Cipher cipher = Cipher.getInstance("AES/CBC/NoPadding");
            int blockSize = cipher.getBlockSize();
            byte[] dataBytes = data.getBytes();
            int plaintextLength = dataBytes.length;

            if (plaintextLength % blockSize != 0) {
                plaintextLength = plaintextLength + (blockSize - (plaintextLength % blockSize));
            }

            byte[] plaintext = new byte[plaintextLength];
            System.arraycopy(dataBytes, 0, plaintext, 0, dataBytes.length);

            SecretKeySpec keyspec = new SecretKeySpec(key.getBytes(), "AES");
            IvParameterSpec ivspec = new IvParameterSpec(iv.getBytes());  // CBC模式，需要一个向量iv，可增加加密算法的强度

            cipher.init(Cipher.ENCRYPT_MODE, keyspec, ivspec);
            byte[] encrypted = cipher.doFinal(plaintext);

            return encode(encrypted).trim(); // BASE64做转码。

        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * @param data 密文
     * @param key  密钥，长度16
     * @param iv   偏移量，长度16
     * @return 明文
     * @author miracle.qu
     * @Description AES算法解密密文
     */
    public static String decryptAEScbc(String data, String key, String iv) throws Exception {
        try {
            byte[] encrypted1 = decode(data);//先用base64解密

            Cipher cipher = Cipher.getInstance("AES/CBC/NoPadding");
            SecretKeySpec keyspec = new SecretKeySpec(key.getBytes(), "AES");
            IvParameterSpec ivspec = new IvParameterSpec(iv.getBytes());

            cipher.init(Cipher.DECRYPT_MODE, keyspec, ivspec);

            byte[] original = cipher.doFinal(encrypted1);
            String originalString = new String(original);
            return originalString.trim();
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 编码
     *
     * @param byteArray
     * @return
     */
    public static String encode(byte[] byteArray) {
        return new String(Base64.getEncoder().encode(byteArray));
    }

    /**
     * 解码
     *
     * @param base64EncodedString
     * @return
     */
    public static byte[] decode(String base64EncodedString) {
        return Base64.getDecoder().decode(base64EncodedString);
    }


    @Test
    public void aesCbcTest() {
        String key = "123456789abcdefg";
        String iv = "0123456789abcdef";
        String text = "aes加密测试";

        try {
            String sText = encryptAEScbc(text, key, iv);
            System.out.println("加密后：" + sText);
            String dText = decryptAEScbc(sText, key, iv);
            System.out.println("解密后：" + dText);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

#### NodeJs

```javascript
var CryptoJS = require("crypto-js");
var key = "123456789abcdefg";
var iv = "0123456789abcdef";

function encrypt(text){
    return CryptoJS.AES.encrypt(text,CryptoJS.enc.Utf8.parse(key),{
        iv:CryptoJS.enc.Utf8.parse(iv),
        mode:CryptoJS.mode.CBC,
        padding:CryptoJS.pad.Pkcs7
    })
}

function decrypt(text){
    var result = CryptoJS.AES.decrypt(text,CryptoJS.enc.Utf8.parse(key),{
        iv:CryptoJS.enc.Utf8.parse(iv),
        mode:CryptoJS.mode.CBC,
        padding:CryptoJS.pad.Pkcs7
    })
    return result.toString(CryptoJS.enc.Utf8)
}

var text=`aes加密测试`;
var encoded=encrypt(text)
console.log(encoded.toString());
console.log('-------------------------------------');
console.log(decrypt(encoded));
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`

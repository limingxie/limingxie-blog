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

#### Golang

```go
package goaes

import (
 "crypto/aes"
 "crypto/cipher"
 "encoding/base64"
 "errors"
 "fmt"
)

/*
 AES-CBC 加密
 text String 待加密的明文
 key String 秘钥
 iv String iv值
 return Base64, err
*/
func EncryptAESCBC(text, key, iv string) (result string, err error) {
 if len(key) != 16 && len(key) != 24 && len(key) != 32 {
  return "", errors.New("key length must be 16、24、32")
 }

 if len(key) != len(iv) {
  return "", errors.New("IV length must equal block size")
 }

 encrypted, err := CBCEncrypter([]byte(text), []byte(key), []byte(iv))
 if err != nil {
  return "", err
 }

 return base64.StdEncoding.EncodeToString(encrypted), nil
}

/*
 AES-CBC 解密
 encrypter base64 待解密的密文
 key string 秘钥
 iv string iv值
 return String, err
*/
func DecryptAESCBC(encrypter, key, iv string) (result string, err error) {
 if len(key) != 16 && len(key) != 24 && len(key) != 32 {
  return "", errors.New("请确保的key的长度是 16、24、32 中一个。")
 }

 if len(key) != len(iv) {
  return "", errors.New("IV length must equal block size")
 }

 encryptByte, err := base64.StdEncoding.DecodeString(encrypter)
 if err != nil {
  return "", err
 }

 decrypted, err := CBCDecrypter(encryptByte, []byte(key), []byte(iv))
 if err != nil {
  return "", err
 }

 return string(decrypted), nil
}

/*
 CBC 加密
 text 待加密的明文
 key 秘钥
*/
func CBCEncrypter(text []byte, key []byte, iv []byte) ([]byte, error) {
 block, err := aes.NewCipher(key)
 if err != nil {
  return nil, err
 }
 // 填充
 paddText := PKCS7Padding(text, block.BlockSize())

 blockMode := cipher.NewCBCEncrypter(block, iv)

 // 加密
 result := make([]byte, len(paddText))
 blockMode.CryptBlocks(result, paddText)
 // 返回密文
 return result, nil
}

/*
 CBC 解密
 encrypter 待解密的密文
 key 秘钥
*/
func CBCDecrypter(encrypter []byte, key []byte, iv []byte) ([]byte, error) {
 block, err := aes.NewCipher(key)
 if err != nil {
  fmt.Println(err)
 }
 blockMode := cipher.NewCBCDecrypter(block, iv)
 result := make([]byte, len(encrypter))
 blockMode.CryptBlocks(result, encrypter)
 // 去除填充
 result = UnPKCS7Padding(result)
 return result, err
}

/*
 PKCS7Padding 填充模式
 text：明文内容
 blockSize：分组块大小
*/
func PKCS7Padding(text []byte, blockSize int) []byte {
 // 计算待填充的长度
 padding := blockSize - len(text)%blockSize
 var paddingText []byte
 if padding == 0 {
  // 已对齐，填充一整块数据，每个数据为 blockSize
  paddingText = bytes.Repeat([]byte{byte(blockSize)}, blockSize)
 } else {
  // 未对齐 填充 padding 个数据，每个数据为 padding
  paddingText = bytes.Repeat([]byte{byte(padding)}, padding)
 }
 return append(text, paddingText...)
}

/*
 去除 PKCS7Padding 填充的数据
 text 待去除填充数据的原文
*/
func UnPKCS7Padding(text []byte) []byte {
 // 取出填充的数据 以此来获得填充数据长度
 unPadding := int(text[len(text)-1])
 return text[:(len(text) - unPadding)]
}

func MainGoAesCBC() {
 key := "123456789abcdefg"
 iv := "0123456789abcdef"
 text := "aes_CBC加密测试"

 // 加密 []byte
 encrypted, _ := CBCEncrypter([]byte(text), []byte(key), []byte(iv))
 fmt.Println(base64.StdEncoding.EncodeToString(encrypted))

 // 解密 []byte
 decrypted, _ := CBCDecrypter([]byte(encrypted), []byte(key), []byte(iv))
 fmt.Println(string(decrypted))

 // 加密 string
 encryptedStr, _ := EncryptAESCBC(text, key, iv)
 fmt.Println(encryptedStr)
 // 解密 string
 decryptStr, _ := DecryptAESCBC(encryptedStr, key, iv)
 fmt.Println(decryptStr)
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`

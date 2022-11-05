---
author: "li_mingxie"
title: "【算法笔记】HASH_算法简单实践"
date: 2022-10-31T23:28:49+08:00
tags: [
    "密码学",
    "hash",
]
categories: [
    "algorithm",
]
---

这篇文章简单的整理了`hash算法的简单实践`。  <!--more-->  

## 1.hash算法概念

我们先看看[维基百科](https://zh.wikipedia.org/wiki/%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B8)的解释。  

```
是一种从任何一种数据中创建小的数字`指纹fingerprint`的方法。  
散列函数把消息或数据压缩成摘要，使得数据量变小，将数据的格式固定下来。  
该函数将数据打乱混合，重新创建一个叫做散列值（hash values，hash codes，hash sums，或hashes）的指纹。  
散列值通常用一个短的随机字母和数字组成的字符串来代表。  
好的散列函数在输入域中很少出现散列冲突。在散列表和数据处理中，不抑制冲突来区别数据，会使得数据库记录更难找到。
```

## 2.hash算法的特点

* 正向快速：给定的明文和hash算法，在有限的资源和有限的时间内能快速的算出hash值。
* 逆向困难：在有限的时间内很难(几乎不可能)逆推明文。
* 输入敏感：原始输入信息修改一点，产生的hash值看起来变化很大。
* 抗冲突：又称为抗碰撞性，就是不同的明文算出相同的hash值的概率要很低(几乎不可能)。就是用一段hash值找出相同的明文的可能性极低。

## 3.目前流行的方法

MD5, SHA-1, SHA-2系列(SHA-224, SHA-256, SHA-384, SHA-512)。  
其中MD5和SHA-1算法已经认为不太安全。  

## 4.简单实例

下面用java实现的简单用例：

```java
import org.junit.Test;
import java.security.MessageDigest;

/**
 * @author : 李明燮
 * @since : 2022/10/31
 * create at : 2022/10/31 下午10:50
 */
public class HashUtil {
    
    //byte转换成16进制
    public static String bytes2HexStr(byte[] bytes) {
        StringBuffer result = new StringBuffer();
        for (byte byt : bytes) {
            result.append(Integer.toString((byt & 0xff) + 0x100, 16).substring(1));
        }
        return result.toString();
    }

    //16进制转byte
    public static byte[] hexStr2HexBytes(String strHex) {
        if (strHex == null || strHex.length() == 0) {
            return null;
        }
        strHex = (strHex.length() == 1) ? "0" + strHex : strHex;
        byte[] arr = new byte[strHex.length() / 2];
        byte[] tmp = strHex.getBytes();
        for (int i = 0; i < (tmp.length / 2); i++) {
            arr[i] = unitBytes(tmp[i * 2], tmp[i * 2 + 1]);
        }
        return arr;
    }

    /**
     * 将两个ASCII字符合成一个字节；
     * 如："EF"--> 0xEF
     *
     * @param src0 byte
     * @param src1 byte
     * @return byte
     */
    public static byte unitBytes(byte src0, byte src1) {
        byte _b0 = Byte.decode("0x" + new String(new byte[]{src0})).byteValue();
        _b0 = (byte) (_b0 << 4);
        byte _b1 = Byte.decode("0x" + new String(new byte[]{src1})).byteValue();
        byte ret = (byte) (_b0 ^ _b1);
        return ret;
    }


    public static String md5(byte[] data) throws Exception {
        //初始化MessageDigest
        MessageDigest md = MessageDigest.getInstance("MD5");
        //执行摘要方法
        byte[] digest = md.digest(data);
        return bytes2HexStr(digest);
    }

    public static String sha(byte[] data) throws Exception {
        //初始化MessageDigest
        MessageDigest md = MessageDigest.getInstance("SHA");
        //执行摘要方法
        byte[] digest = md.digest(data);
        return bytes2HexStr(digest);
    }

    public static String sha256(byte[] data) throws Exception {
        //初始化MessageDigest
        MessageDigest md = MessageDigest.getInstance("SHA-256");
        //执行摘要方法
        byte[] digest = md.digest(data);
        return bytes2HexStr(digest);
    }

    public static String sha512(byte[] data) throws Exception {
        //初始化MessageDigest
        MessageDigest md = MessageDigest.getInstance("SHA-512");
        //执行摘要方法
        byte[] digest = md.digest(data);
        return bytes2HexStr(digest);
    }

    @Test
    public void testHash() throws Exception {
        String src = "123456";

        try {
            String md5 = md5(src.getBytes());
            System.out.println(md5);

            String sha = sha(src.getBytes());
            System.out.println(sha);

            String sha256 = sha256(src.getBytes());
            System.out.println(sha256);

            String sha512 = sha512(src.getBytes());
            System.out.println(sha512);

        } catch (Exception e) {
            e.printStackTrace();
        }

        System.out.println("-------------------------");

        try {
            byte[] arrByte = hexStr2HexBytes(src);
            String md5 = md5(arrByte);
            System.out.println(md5);

            String sha = sha(arrByte);
            System.out.println(sha);

            String sha256 = sha256(arrByte);
            System.out.println(sha256);

            String sha512 = sha512(arrByte);
            System.out.println(sha512);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

执行结果

```
e10adc3949ba59abbe56e057f20f883e
7c4a8d09ca3762af61e59520943dc26494f8941b
8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
ba3253876aed6bc22d4a6ff53d8406c6ad864195ed144ab5c87621b6c233b548baeae6956df346ec8c17f5ea10f35ee3cbc514797ed7ddd3145464e2a0bab413
-------------------------
ae1fa6209a246b8b2f2cd2d21be8f2e1
d54093641b8ab5efe6d9c8dca9fb39112cb1722a
bf7cbe09d71a1bcc373ab9a764917f730a6ed951ffa1a7399b7abd8f8fd73cb4
45341a09a5df1bec7ceac3716e32692a8029903178b1ce8bc57c2ac2c11c82100553ae1fcb99118fa7f5a62f524e162e80e0e1533a4e6177b3fa9d830f48bd37
```

看看go的实现：  

```go
func HASH(text string, hashType string, isHex bool) (string, error) {
 var hashInstance hash.Hash
 switch hashType {
 case "md4":
  hashInstance = md4.New()
 case "md5":
  hashInstance = md5.New()
 case "sha1":
  hashInstance = sha1.New()
 case "ripemd160":
  hashInstance = ripemd160.New()
 case "sha256":
  hashInstance = sha256.New()
 case "sha512":
  hashInstance = sha512.New()
 }
 if isHex {
  arr, err := hex.DecodeString(text)
  if err != nil {
   return "", err
  }
  hashInstance.Write(arr)
 } else {
  hashInstance.Write([]byte(text))
 }

 bytes := hashInstance.Sum(nil)
 return fmt.Sprintf("%x", bytes), nil
}

func Hash256Double(text string, isHex bool) ([]byte, error) {
 hashInstance := sha256.New()
 if isHex {
  arr, err := hex.DecodeString(text)
  if err != nil {
   return nil, err
  }
  hashInstance.Write(arr)
 } else {
  hashInstance.Write([]byte(text))
 }

 bytes := hashInstance.Sum(nil)
 hashInstance.Reset()
 bytes = hashInstance.Sum(nil)
 return bytes, nil
}

func Hash256DoubleString(text string, isHex bool) (string, error) {
 bytes, err := Hash256Double(text, isHex)
 if err != nil {
  return "", err
 }
 return fmt.Sprintf("%x", bytes), nil
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`

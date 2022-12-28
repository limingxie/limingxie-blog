---
author: "li_mingxie"
title: "【区块链笔记】BTC_地址计算(13)"
date: 2022-11-06T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "BTC",
    "bitcoin",
]
categories: [
    "blockchain",
    "btc",
]
---

今天看了有关算`比特币地址的算法`，简单的整理了一下。  <!--more-->  

## 1.计算方法

比特币地址生成步骤  
从私钥生成开始经过非对称加密算法、哈希算法(SHA-256和RIPEMD-160)、以及Base58编码方式，最终产生公开的比特币地址。

第一步，随机选取一个32字节的数、大小介于1 ~
0xFFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFE BAAE DCE6 AF48 A03B BFD2 5E8C D036 4141之间，作为私钥。
18E14A7B6A307F426A94F8114701E7C8E774E7F9A47E2C2035DB29A206321725

第二步，使用椭圆曲线加密算法（ECDSA-secp256k1）计算私钥所对应的非压缩公钥。  
(共65字节， 1字节 0x04, 32字节为x坐标，32字节为y坐标）关于公钥压缩、非压缩的问题另文说明。

0450863AD64A87AE8A2FE83C1AF1A8403CB53F53E486D8511DAD8A04887E5B
23522CD470243453A299FA9E77237716103ABC11A1DF38855ED6F2EE187E9C582BA6

第三步，计算公钥的 SHA-256 哈希值
600FFE422B4E00731A59557A5CCA46CC183944191006324A447BDB2D98D4B408

第四步，取上一步结果，计算 RIPEMD-160 哈希值
010966776006953D5567439E5E39F86A0D273BEE

第五步，取上一步结果，前面加入地址版本号（比特币主网版本号“0x00”）
00010966776006953D5567439E5E39F86A0D273BEE

第六步，取上一步结果，计算 SHA-256 哈希值
445C7A8007A93D8733188288BB320A8FE2DEBD2AE1B47F0F50BC10BAE845C094

第七步，取上一步结果，再计算一下 SHA-256 哈希值
D61967F63C7DD183914A4AE452C9F6AD5D462CE3D277798075B107615C1A8A30

第八步，取上一步结果的前4个字节（8位十六进制）
D61967F6

第九步，把这4个字节加在第五步的结果后面，作为校验（这就是比特币地址的16进制形态）。
00010966776006953D5567439E5E39F86A0D273BEED61967F6

第十步，用base58表示法变换一下地址（这就是最常见的比特币地址形态）。
16UwLL9Risc3QfPqBUvKofHmBQ7wMtjvM

说明: 有些数字以"0x"开头，意思是此数字使用十六进制表示法。"0x"本身没有任何含义，它是C语言流传下来的，约定俗成的写法，比如0xA就是十进制的10。另外，1个字节 = 8位二进制 = 2位十六进制）。

1BLvJjhACw8keNeqrkXTbMHYTP1cH4zoDk

详细的内容可以参考这里：<https://gobittest.appspot.com/Address>  

## 2.模拟代码

在早起的交易中找到了公钥和地址，用代码一起算一下。

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_header_cal.png)  
![blockchain_header_cal](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_header_cal.png)  

### 参考代码

```go
func HASH256(text string, isHex bool) (string, error) {
 sha256Instance := sha256.New()
 if isHex {
  arr, err := hex.DecodeString(text)
  if err != nil {
   return "", err
  }
  sha256Instance.Write(arr)
 } else {
  sha256Instance.Write([]byte(text))
 }
 ciphertext := sha256Instance.Sum(nil)
 return fmt.Sprintf("%x", ciphertext), nil
}

func RipMD160(text string, isHex bool) (string, error) {
 ripemd160Instance := ripemd160.New()
 if isHex {
  arr, err := hex.DecodeString(text)
  if err != nil {
   return "", err
  }
  ripemd160Instance.Write(arr)
 } else {
  ripemd160Instance.Write([]byte(text))
 }
 hashBytes := ripemd160Instance.Sum(nil)
 hashString := fmt.Sprintf("%x", hashBytes)
 return hashString, nil
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
 hashInstance.Write(bytes)
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

func GenerateAddress(pubKey string, nettype int) (string, error) {
 //1.判断公钥的有效性
 if len(pubKey) != 130 && len(pubKey) != 66 {
  return "", errors.New("公钥输入的长度不合法！")
 }
 //2.计算公钥sha256
 hashString, err := HASH256(pubKey, true)
 if err != nil {
  return "", err
 }

 //3.ripemd160
 ripemd160String, err := RipMD160(hashString, true)
 if err != nil {
  return "", err
 }

 //4.添加网络id号，比特币主网0x00 testnet 0x6f
 prefix := ""
 switch nettype {
 case 0:
  prefix = "00"
 case 1:
  prefix = "6f"
 case 2:
  prefix = "34"
 }

 versionString := prefix + ripemd160String

 //5.计算hash 2次
 sha256DoubleString, err := Hash256DoubleString(versionString, true)
 if err != nil {
  return "", err
 }

 //6.获取校验码
 rs := []rune(sha256DoubleString)
 checknum := string(rs[:8])

 //7.形成16进制比特币地址
 addrHex := versionString + checknum

 //8.对16进制地址进行base58编码
 arr, err := hex.DecodeString(addrHex)
 if err != nil {
  return "", err
 }
 //9.base58
 addrBase58 := Base58Encode(arr)
 result := fmt.Sprintf("%s \n", addrBase58)

 return result, nil
}

func main() {
 pubKey := "0417a4a4037defe1c30da0c3ed0ca096ae99e846a10c594b0224cb4c54cc0d087028e27b218c422d095a11c8b9a34e2bfdb2f112311adea31245e190e3775cc5b7"
 address, _ := GenerateAddress(pubKey, 0)
 fmt.Println(address) //1Lr12b7kWNXbRiwPEGq65Y19NuKYXyPJiK
}
```

#### base58

```go
import (
 "math/big"
)

const (
 // alphabet is the modified base58 alphabet used by Bitcoin.
 alphabet = "123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz"

 alphabetIdx0 = '1'
)

var b58 = [256]byte{
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 0, 1, 2, 3, 4, 5, 6,
 7, 8, 255, 255, 255, 255, 255, 255,
 255, 9, 10, 11, 12, 13, 14, 15,
 16, 255, 17, 18, 19, 20, 21, 255,
 22, 23, 24, 25, 26, 27, 28, 29,
 30, 31, 32, 255, 255, 255, 255, 255,
 255, 33, 34, 35, 36, 37, 38, 39,
 40, 41, 42, 43, 255, 44, 45, 46,
 47, 48, 49, 50, 51, 52, 53, 54,
 55, 56, 57, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
 255, 255, 255, 255, 255, 255, 255, 255,
}

//go:generate go run genalphabet.go

var bigRadix = big.NewInt(58)
var bigZero = big.NewInt(0)

// Decode decodes a modified base58 string to a byte slice.
func Base58Decode(b string) []byte {
 answer := big.NewInt(0)
 j := big.NewInt(1)

 scratch := new(big.Int)
 for i := len(b) - 1; i >= 0; i-- {
  tmp := b58[b[i]]
  if tmp == 255 {
   return []byte("")
  }
  scratch.SetInt64(int64(tmp))
  scratch.Mul(j, scratch)
  answer.Add(answer, scratch)
  j.Mul(j, bigRadix)
 }

 tmpval := answer.Bytes()

 var numZeros int
 for numZeros = 0; numZeros < len(b); numZeros++ {
  if b[numZeros] != alphabetIdx0 {
   break
  }
 }
 flen := numZeros + len(tmpval)
 val := make([]byte, flen)
 copy(val[numZeros:], tmpval)

 return val
}

// Encode encodes a byte slice to a modified base58 string.
func Base58Encode(b []byte) string {
 x := new(big.Int)
 x.SetBytes(b)

 answer := make([]byte, 0, len(b)*136/100)
 for x.Cmp(bigZero) > 0 {
  mod := new(big.Int)
  x.DivMod(x, bigRadix, mod)
  answer = append(answer, alphabet[mod.Int64()])
 }

 // leading zero bytes
 for _, i := range b {
  if i != 0 {
   break
  }
  answer = append(answer, alphabetIdx0)
 }

 // reverse
 alen := len(answer)
 for i := 0; i < alen/2; i++ {
  answer[i], answer[alen-1-i] = answer[alen-1-i], answer[i]
 }

 return string(answer)
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`

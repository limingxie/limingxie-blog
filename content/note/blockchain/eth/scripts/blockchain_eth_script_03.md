---
author: "li_mingxie"
title: "【区块链笔记】ETH_script_合约测试脚本(03)"
date: 2022-12-09T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
    "eth",
    "web3",
]
---

这篇文章主要是`以太坊script_合约测试脚本`。  <!--more-->  

## 1.mocha简介

mocha是JavaScript的一个单元测试框架，既可以在浏览器环境中运行，也可以在node.js环境下运行。  
我们只需要编写测试用例，mocha会将测试自动运行并给出测试结果。  

mocha的主要特点有:

* 既可以测试简单的JavaScript函数，又可以测试异步代码;
* 可以自动运行所有测试，也可以只运行特定的测试;
* 可以支持 before、after、beforeEach 和 afterEach 来编写初始化代码。

```bash
npm install mocha --save-dev
```

## 2.测试实例

**sum.js**

```js
module.exports = function (...rest) {
    var sum = 0;
    for (let n of rest) {
        sum += n;
    }
    return sum;
};
```

**test_sum.js**

```js
const assert = require('assert');
const sum = require('./sum');
assert.strictEqual(sum(), 0);
assert.strictEqual(sum(1), 1);
assert.strictEqual(sum(1, 2), 3);
assert.strictEqual(sum(1, 2, 3), 6);
```

**执行**

```bash
node ./test/test_sum.js
```

#### 使用mocha

**test_sum_mocha.js**

```js
const assert = require('assert');
const sum = require('./sum');
describe('#sum.js', () => {
    describe('#sum()', () => {
        it('sum() should return 0', () => {
            assert.strictEqual(sum(), 0);
        });
        it('sum(1) should return 1', () => {
            assert.strictEqual(sum(1), 1);
        });
        it('sum(1, 2) should return 3', () => {
            assert.strictEqual(sum(1, 2), 3);
        });
        it('sum(1, 2, 3) should return 6', () => {
            assert.strictEqual(sum(1, 2, 3), 6);
        });
    });
});
```

**执行**

```bash
$ ./node_modules/mocha/bin/_mocha ./test/test_sum_mocha.js

  #sum.js
    #sum()
      ✔ sum() should return 0
      ✔ sum(1) should return 1
      ✔ sum(1, 2) should return 3
      ✔ sum(1, 2, 3) should return 6


  4 passing (8ms)
```

## 3.测试合约

**car_test.js**

```js
const path = require('path');
const assert = require('assert');
const ganache = require('ganache-cli');
const Web3 = require('web3');
// 1. 配置 provider
const web3 = new Web3(ganache.provider());
// 2. 拿到 abi 和 bytecode
const contractPath = path.resolve(__dirname, '../compiled/Car.json');

const compileSolCode = require(contractPath);
const abi = compileSolCode.Car.abi;
const bytecode = compileSolCode.Car.evm.bytecode.object.toString();

let accounts;
let contract;
const initialBrand = 'BMW';

describe('contract start', () => {
    before (()=>{
        console.log('测试开始====>');
    });
    after(()=>{
        console.log('====>测试结束！');
    });
    afterEach(async () => {
    });
    // 3. 每次跑单测时需要部署全新的合约实例，起到隔离的作用 beforeEach(async () => {
    beforeEach(async () => {
        accounts = await web3.eth.getAccounts();
        contract = await new web3.eth.Contract(abi)
            .deploy({ data: bytecode, arguments: [initialBrand, 1000000] })
            .send({ from: accounts[0], gas: 1000000 });
    });
    // 4. 编写单元测试
    it('deployed contract', () => {
        assert.ok(contract.options.address);
    });
    it('should has initial brand', async () => {
        const brand = await contract.methods.getBrand().call();
        assert.equal(brand, initialBrand);
    });

    it('can change the brand', async () => {
        const newBrand = 'Benz';
        await contract.methods.setBrand(newBrand).send({ from: accounts[0] });
        const brand = await contract.methods.getBrand().call();
        assert.equal(brand, newBrand);
    });
});

```

**执行**

```bash
$ ./node_modules/mocha/bin/_mocha 

  contract start
测试开始====>
    ✔ deployed contract
    ✔ should has initial brand
    ✔ can change the brand (75ms)
====>测试结束！

  3 passing (418ms)
```

## 4.package.json添加命令

可以添加快捷方式的命令。简化开发中的操作。

```json
"scripts": {
    "compile": "node scripts/compile.js",
    "pretest": "npm run compile",
    "test": "mocha tests/",
    "predeploy": "npm run compile",
    "deploy": "node scripts/deploy.js"
},
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`

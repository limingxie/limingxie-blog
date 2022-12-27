---
author: "li_mingxie"
title: "【区块链笔记】ETH_代币标准(04)"
date: 2022-11-25T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
]
---


这篇文章简单的整理了`以太坊代币标准`。  <!--more-->  

## 1.代币标准

以下是以太坊上最受欢迎的一些代币标准：

* [ERC-20](https://ethereum.org/zh/developers/docs/standards/tokens/erc-20/) - 同质化（可互换）代币的标准接口，比如投票代币、质押代币或虚拟货币
* [ERC-721](https://ethereum.org/zh/developers/docs/standards/tokens/erc-721/) - 非同质化代币的标准接口，比如艺术作品或歌曲的契约。
* [ERC-777](https://ethereum.org/zh/developers/docs/standards/tokens/erc-777/) - ERC-777 使人们能够通过代币上创建额外的功能，  
    例如用于改善交易私密性的混合合约，或是在您不慎丢失私钥时的紧急恢复功能。
* [ERC-1155](https://ethereum.org/zh/developers/docs/standards/tokens/erc-1155/) - ERC-1155 允许更有效的交易和打包交易，从而节省燃料成本。  
    此代币标准允许创建实用代币（例如 $BNB 或 $BAT）和加密朋克之类的非同质化代币。
* [ERC-4626](https://ethereum.org/zh/developers/docs/standards/tokens/erc-4626/) - 一个代币化的资金库标准，旨在优化和统一收益资金库的技术参数。

## 2.ERC-20解析

```sol
pragma solidity ^0.4.16;

interface tokenRecipient {
    function receiveApproval(
        address _from,
        uint256 _value,
        address _token,
        bytes _extraData
    ) external;
}

contract TokenERC20 {
    // Public variables of the token
    string public name;         //代币名称
    string public symbol;       //代币符号
    uint8 public decimals = 18; //代币小数点
    // 18 decimals is the strongly suggested default, avoid changing it
    uint256 public totalSupply; //代币总金额

    // This creates an array with all balances
    mapping(address => uint256) public balanceOf;   //账户管理
    mapping(address => mapping(address => uint256)) public allowance; //账户授权，每个账户都可以授权给所有账户

    // This generates a public event on the blockchain that will notify clients
    event Transfer(address indexed from, address indexed to, uint256 value);

    // This generates a public event on the blockchain that will notify clients
    // 确认交易
    event Approval(
        address indexed _owner,
        address indexed _spender,
        uint256 _value
    );

    // This notifies clients about the amount burnt
    event Burn(address indexed from, uint256 value); //销毁代币

    /**
     * Constructor function
     *
     * Initializes contract with initial supply tokens to the creator of the contract
     */
    //旧版本，初始化没有用到constructor()
    function TokenERC20(
        uint256 initialSupply,  //总数
        string tokenName,       //代币名称
        string tokenSymbol      //代币符号
    ) public {
        //小数点位数最终是使用整数做计算
        totalSupply = initialSupply * 10**uint256(decimals); /*Update total supply with the decimal amount*/
        balanceOf[msg.sender] = totalSupply;    // Give the creator all initial tokens
        name = tokenName;                       // Set the name for display purposes
        symbol = tokenSymbol;                   // Set the symbol for display purposes
    }

    /**
     * Internal transfer, only can be called by this contract
     */
    //只能在内部调用的transfer
    function _transfer(
        address _from,
        address _to,
        uint256 _value
    ) internal {
        // Prevent transfer to 0x0 address. Use burn() instead
        // 防止对0地址转币
        require(_to != 0x0);
        // Check if the sender has enough check金额
        require(balanceOf[_from] >= _value);
        // Check for overflows  防止溢出
        require(balanceOf[_to] + _value >= balanceOf[_to]);
        // Save this for an assertion in the future
        uint256 previousBalances = balanceOf[_from] + balanceOf[_to]; //为下面的总和验证先做好计算
        // Subtract from the sender
        balanceOf[_from] -= _value;
        // Add the same to the recipient
        balanceOf[_to] += _value;
        emit Transfer(_from, _to, _value);
        // Asserts are used to use static analysis to find bugs in your code. They should never fail
        assert(balanceOf[_from] + balanceOf[_to] == previousBalances); //一定要满足总和不变
    }

    /**
     * Transfer tokens 
     *
     * Send `_value` tokens to `_to` from your account 
     *
     * @param _to The address of the recipient
     * @param _value the amount to send 
     */
    //对外提供的transfer
    function transfer(address _to, uint256 _value)
        public
        returns (bool success)
    {
        _transfer(msg.sender, _to, _value);
        return true;
    }

    /**
     * Transfer tokens from other address
     *
     * Send `_value` tokens to `_to` on behalf of `_from`
     *
     * @param _from The address of the sender
     * @param _to The address of the recipient
     * @param _value the amount to send
     */
    // 牵扯到授权的转账
    function transferFrom(
        address _from,
        address _to,
        uint256 _value
    ) public returns (bool success) {
        require(_value <= allowance[_from][msg.sender]); // Check allowance
        allowance[_from][msg.sender] -= _value;
        _transfer(_from, _to, _value);
        return true;
    }

    /**
     * Set allowance for other address
     *
     * Allows `_spender` to spend no more than `_value` tokens on your behalf
     *
     * @param _spender The address authorized to spend
     * @param _value the max amount they can spend
     */
    // 授权额度给账户
    function approve(address _spender, uint256 _value)
        public
        returns (bool success)
    {
        allowance[msg.sender][_spender] = _value;
        emit Approval(msg.sender, _spender, _value);
        return true;
    }

    /**
     * Set allowance for other address and notify
     *
     * Allows `_spender` to spend no more than `_value` tokens on your behalf, and then ping the contract about it
     *
     * @param _spender The address authorized to spend
     * @param _value the max amount they can spend
     * @param _extraData some extra information to send to the approved contract
     */
    function approveAndCall(
        address _spender,
        uint256 _value,
        bytes _extraData
    ) public returns (bool success) {
        tokenRecipient spender = tokenRecipient(_spender);
        if (approve(_spender, _value)) {
            spender.receiveApproval(msg.sender, _value, this, _extraData);
            return true;
        }
    }

    /**
     * Destroy tokens
     *
     * Remove `_value` tokens from the system irreversibly
     *
     * @param _value the amount of money to burn
     */
    // 销毁代币
    function burn(uint256 _value) public returns (bool success) {
        require(balanceOf[msg.sender] >= _value);   // Check if the sender has enough
        balanceOf[msg.sender] -= _value;            // Subtract from the sender
        totalSupply -= _value;                      // Updates totalSupply
        emit Burn(msg.sender, _value);
        return true;
    }

    /**
     * Destroy tokens from other account
     *
     * Remove `_value` tokens from the system irreversibly on behalf of `_from`.
     *
     * @param _from the address of the sender
     * @param _value the amount of money to burn
     */
    //代替别人授权销毁
    function burnFrom(address _from, uint256 _value)
        public
        returns (bool success)
    {
        require(balanceOf[_from] >= _value);                // Check if the targeted balance is enough
        require(_value <= allowance[_from][msg.sender]);    // Check allowance
        balanceOf[_from] -= _value;                         // Subtract from the targeted balance
        allowance[_from][msg.sender] -= _value;             // Subtract from the sender's allowance
        totalSupply -= _value;                              // Update totalSupply
        emit Burn(_from, _value);
        return true;
    }
}

```

最新的Sample可以参考：  
<https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol>  

<https://solidity-by-example.org/app/erc20/>

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`

---
author: "li_mingxie"
title: "【区块链笔记】ETH_Solidity简单投票合约(05)"
date: 2022-11-26T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
    "eth",
    "solidity",
]
---


这篇文章简单的整理了`Solidity简单投票合约`。  <!--more-->  

## 1.简单的投票合约

* 电子投票的主要问题是如何将投票权分配给正确的人员以及如何防止被操纵。
    这个合约展示了如何进行委托投票，同时，计票又是自动和完全透明的
* 为每个(投票)表决创建一份合约，然后作为合约的创造者——即主席，将给予每个独立的地址以投票权
* 地址后面的人可以选择自己投票，或者委托给他们信任的人来投票
* 在投票时间结束时，winningProposal() 将返回获得最多投票的提案

## 2.代码

```sol
pragma solidity ^0.4.0;

contract Ballot {
    
    struct Voter {
       uint weight;
       bool voted;
       uint8 vote;
       address delegate; 
    }
    struct Proposal {
        uint voteCount;
    }

    address chairperson;
    mapping(address => Voter) voters;
    Proposal[] proposals;

    function Ballot(uint8 _numProposals) public {
        chairperson = msg.sender;
        voters[chairperson].weight = 1;
        proposals.length = _numProposals;
    }

    //授权投票
    function giveRightToVote(address toVoter) public {
        if (msg.sender != chairperson || voters[toVoter].voted) return;
        voters[toVoter].weight = 1;
    }

    //委托投票
    function delegate(address to) public {
        Voter storage sender = voters[msg.sender];
        if (sender.voted) return;
        while (voters[to].delegate != address(0) && voters[to].delegate != msg.sender)
            to = voters[to].delegate;
        if (to == msg.sender) return;
        sender.voted = true;
        sender.delegate = to;
        Voter storage delegateTo = voters[to];
        if (delegateTo.voted)
            proposals[delegateTo.vote].voteCount += sender.weight;
        else
            delegateTo.weight +=sender.weight;
    }

    function vote(uint8 toProposal) public {
        Voter storage sender = voters[msg.sender];
        if (sender.voted || toProposal > proposals.length) return;
        sender.voted = true;
        sender.vote = toProposal;
        proposals[toProposal].voteCount += sender.weight;
    }

    function winningProposal() public constant returns (uint _winningProposal){
        uint256 winningVoteCount = 0;
        for(uint prop = 0; prop < proposals.length; prop++)
            if (proposals[prop].voteCount > winningVoteCount){
                winningVoteCount = proposals[prop].voteCount;
                _winningProposal = prop;
            }
    }
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`

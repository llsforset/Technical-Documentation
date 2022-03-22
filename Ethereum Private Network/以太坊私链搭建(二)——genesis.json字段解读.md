genesis.json文件用于配置生成以太坊私链网络的创世区块，当我们需要去创建一个创世区块时，我们可以通过修改genesis.json文件内的初始参数将这些数据写入创世区块。下面是以太坊官方文档给出的一个例子：

```json
{
  "config": {
    "chainId": 15,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "ethash": {}
  },
  "difficulty": "1",
  "gasLimit": "8000000",
  "alloc": {
    "7df9a875a174b3bc565e6424a0050ebc1b2d1d82": { "balance": "300000" },
    "f41c74c9ae680c1aa78f42e5647a62f353b7bdde": { "balance": "400000" }
  }
}
```



# 字段解读

**config**

> config是决定私链设置的核心配置。
>
> **chainId：**链ID，用于标识当前这条链，主要用于重放保护。
>
> **homesteadBlock：**以太坊“家园”版本启用的区块高度，homesteadBlock被设置为0则意味着我们将直接使用以太坊的“家园”版本，在以太坊主网中此设置为0.
>
> **eip150Block：**第150号以太坊改进提案(EIP150)启用的区块高度，该提案主要用于解决因denial-of-service问题导致gas价格提高的问题主网中其启用高度为2463000。
>
> **eip155Block：**EIP155提案启用的区块高度，该提案主要是为了预防重放攻击。
>
> **eip158Block：**EIP158提案启用的区块高度，该提案的提出主要是为了改变以太坊客户端处理空账户的方式。在EIP158提案中以太坊客户端会将空账户视为不存在的对象，这能有效节省区块链上的存储空间。
>
> **byzantiumBlock：**拜占庭硬分叉启用的区块高度，最大的改动是将出块奖励从5ETH减少到了3ETH，主网中该分叉的启用区块高度为4370000。
>
> **constantinopleBlock：**君士坦丁堡硬分叉启用的区块高度，该分叉的目的是提高以太坊的运行效率，并移除/推迟难度炸弹，同时将出块奖励从拜占庭硬分叉后的3ETH减少为2ETH。主网中君士坦丁堡硬分叉的启用区块高度为7280000。
>
> **petersburgBlock：**圣彼得堡硬分叉启用的区块高度，该硬分叉是为解决君士坦丁堡硬分叉中的安全漏洞问题，将EIP1283提案移除。主网中圣彼得堡和君士坦丁堡分叉同时激活，区块高度为7280000。
>
> **ethash**：标识当前私链使用的共识算法为ethash。

**mixhash**

> 一个256为的哈希散列，结合nonce一同证明已在此区块上执行了足够量的计算(POW)。nonce和mixhash的组合必须满足黄皮书4.3.4中描述的数学条件，即区块头有效性(44)。它允许验证该区块是否被加密挖掘。

 **nonce**

> 一个用于标记发送方发送的交易数量的值，是一个64位哈希值，与**mixhash**值一起证明已在区块上进行了足够量的计算。

**difficulty**

> 出块难度，对应发现该区块的nonce期间所应用的难度级别，难度越高，矿工发现有效区块所需的计算就越多。该值主要用于控制出块时间，使得出块效率保持在目标范围之内。

**alloc**

> 预定义的钱包列表，可以通过该字段预先在指定钱包地址内预存一定数量得原生代币。这是以太坊预售期间所执行得特定功能。

**coinbase**

> 一个160位地址，用于标识出块奖励的收益地址。

**timestamp**

> 时间戳，相当于Unix中的time()函数的输出。引入时间戳的机制能够在时间方面实现出块的稳态，时间戳还允许验证出块顺序。(黄皮书，4.3.4.(43))。

**parentHash**

> 整个父块头的Keccak256位哈希(包括父块头的nonce和mixhash)，若区块是创世区块，则该值为0。

**extraData**

> 一个可以自由设置的选项，最大32个byte。该字段用于将数据保存在区块链上

**gasLimit**

> 每个区块的gas消耗限制。每个区块所消耗的gas不能超过这个值。



# 参考资料

https://github.com/ethereum/go-ethereum/tree/feeccdf4ec1084b38dac112ff4f86809efd7c0e5/params

[Ethereum private network configuration guide. · GitHub](https://gist.github.com/0mkara/b953cc2585b18ee098cd)

第一个为以太坊相关源码，第二个为genesis.json文件解析。

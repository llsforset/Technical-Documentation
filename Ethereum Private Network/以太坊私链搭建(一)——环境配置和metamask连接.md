基于某些需求我需要将一个以太坊网络运行在自己的服务器上，因此在查阅了相关资料后，我在自己的服务器上搭建了一个私链。搭建一个以太坊私链主要有两步：①安装geth；②私链环境配置和启动。

服务器系统版本：Ubuntu Server 18.04.1 LTS 64位；geth版本：1.10.16-stable-20356e7

# 一、安装geth

geth(go-ethereum)是以太坊官方社区开发的开源客户端，我们可以通过geth命令搭建以太坊私链或者通过geth客户端的控制台与以太坊网络进行交互。
geth的安装方式有很多，但是ubuntu下安装geth还是比较方便的，不需要翻墙，在终端执行下面的命令即可(需要root权限，需要登陆root账户或在每个命令前面+sudo)。

```bash
apt-get install software-properties-common
add-apt-repository -y ppa:ethereum/ethereum
apt-get update
apt-get install ethereum
```

安装完后可以输入geth -h查看命令帮助，能正常显示则安装完成。



# 二、环境配置

首先第一步我们需要创建一个文件夹用来存放我们的私链项目。

```bash
mkdir /home/lls/FangChain
mkdir /home/lls/Fangchain/data
```

项目目录的名称和位置根据需要自行变更，但是需要注意在对私链进行初始化的时候需要指定数据存储位置，能对应上即可，data目录用于存储私链数据，包括账本信息、用户私钥等。


接着我们需要创建一个私链配置json文件用于配置区块链的“创世”状态，这一步的目的是根据需求对私链的状态进行初始化。

```bash
cd /home/lls/FangChain/
```

跳转到项目目录下，创建配置文件genesis.json。

```bash
vi genesis.json
```


编辑文件内容，这里用以太坊官网给的例子，输入内容如下：

```json
{
  "config": {
    "chainId": 15,    //私链的ID，每条私链的ID应该是唯一的
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "ethash": {}
  },
  "difficulty": "1",    //出块难度，数值越大，矿工挖矿出块的难度越大
  "gasLimit": "8000000",    //每个区块最大的gas消耗
  "alloc": {    //资产配置初始化，提前定义哪些地址所拥有的原生代币的数量，单位是wei
    "7df9a875a174b3bc565e6424a0050ebc1b2d1d82": { "balance": "300000" },
    "f41c74c9ae680c1aa78f42e5647a62f353b7bdde": { "balance": "400000" }
  }
}
```

一般来说，在上述文件中需要我们进行配置的是链ID、出块难度和初始资产。**这里记得将genesis.json文件内的注释删除，json不允许添加注释**，否则在下面执行配置初始化命令会出错。
文件保存后执行私链配置初始化命令。

```bash
geth init --datadir data genesis.json
```

其中--datadir字段用于配置私链数据的存储位置，这里存在data文件夹。输入命令后等待片刻，看到“Successfully wrote genesis state”的日志则表示配置完成。
当配置完私链的初始状态后就可以通过geth命令启动私链了，执行以下命令。

```bash
geth --datadir data --networkid 15 --http --http.addr 0.0.0.0 --http.port 8545 --http.corsdomain "*" --port 30305 --allow-insecure-unlock console 2>>geth.log
```

--datadir字段用于指示私链数据的存储位置，即工作目录；

--networkid字段用于配置私链id，需要与genesis.json文件内预定义的一致；

--http字段用于启用HTTP-RPC服务，主要应用于与前端页面的交互；

--http.addr字段表示节点接受的http连接的地址，0.0.0.0表示可以接受所有ip地址的http请求；

--http.port字段用于指定监听端口，默认是8545；

--http.corsdomain字段表示允许跨域请求的域名列表，“*”表示允许所有的跨域请求，不开启的话metamask钱包可能无法连接上搭建的私链；

--port字段用于指定节点之间通信的端口，默认是30303；

--allow-insecure-unlock表示允许不安全的账户解锁行为，开启这个选项后通过http连接到私链的钱包才能解锁账户进行转账操作；

console表示在运行私链节点的同时开启控制台，这样就可以在监控私链节点状态的同时对节点进行操作。因为geth命令运行完后会不断弹出监控日志影响到控制台的使用，因此在命令最后添加2>>geth.log就可以让监控日志输入到当前目录的geth.log文件中而不影响控制台的使用，然后在新开一个终端用tail -f geth.log的命令实时监控节点的日志即可。(如果不愿意这么麻烦可以把2>>geth.log删除)



# 三、用MetaMask钱包连接私链

若要用metamask钱包连接私链节点，则需要在metamask钱包中依次点击“设定-网络-添加网络”进行私链网络的添加，所输入的信息如下：

网络名称：FangChain

新的RPC URL：http://81.72.166.130：8545

链ID：15

Currency Symbol：FangB

区块链浏览器：(先不填)

其中网络名称原生代币的标签根据需要可以自行设置，RPC URL的格式为http://服务器地址:http端口号 (如http://81.72.166.130:8545)，链ID即为networkid中定义的id。当添加完私链网络后，我们就可以通过metamask钱包操作我们在私链上的账户，并可以通过remix将智能合约部署到私链上。

需要注意的是，如果你使用的是云服务器，则需要在云服务器的服务器管理界面开启上述配置中的对应的端口，尤其是http.port对应的端口，否则metamask钱包无法通过该端口连接上私链节点。



# 踩坑点

--http，--http.addr，--http.port和--http.corsdomain是较新版本的geth才启用的字段，老版本对应的字段分别为--rpc，--rpcaddr，--rpcport和rpccorsdomain，根据实际版本进行替换即可。
而在新版本中如果你用的是websocket协议与节点交互，则对应的字段是--ws，--wsaddr，--ws.port和--ws.origins，具体命令详情可以在geth控制台中使用geth -h命令进行查看。
--port端口最好自行指定一个别的端口，因为8545端口为默认端口，在使用默认端口，且启动私链时允许所有ip地址的http请求和所有跨域请求的情况下，容易被机器人攻击，在使用默认端口地情况下节点容易被高频率地访问，而修改端口可以有效避免这种情况。

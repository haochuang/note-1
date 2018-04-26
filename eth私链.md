## 安装geth

## 配置创世区块文件
 自定义创世区块文件，将创世区块文件写入一个json文件，并命名，如 genesis.json
 在home 目录下新建一个文件夹，并命名，例如privatechain, 将编译的json文件保存在其中，供使用，

```
    {
       "coinbase": "0x0000000000000000000000000000000000000000",
       "config": {
              "homesteadBlock": 5
        },
       "difficulty": "0x20000",
       "extraData": "0x",
       "gasLimit": "0x2FEFD8",
       "mixhash": "0x00000000000000000000000000000000000000647572616c65787365646c6578",
       "nonce": "0x0",
       "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
       "timestamp": "0x00",
       "alloc": {
              "dbdbdb2cbd23b783741e8d7fcf51e459b497e4a6":
              {
                  "balance":"100000000000000000000000000000"
              }
       }
    }
```

- 打开cmd，运行命令
```
geth --datadir "C:\Users\Ben\Desktop\privatechain\data0" init "C:\Users\Ben\Desktop\privatechain\genesis.json"
```

![](./_image/2018-04-16-16-30-21.jpg)

此时创世完成！

## 创建账户：

为了在私链上做实验，我们还需要在私链上建立自己的账户

Windows命令行键入

```
geth --datadir "C:\Users\Ben\Desktop\privatechain\data0" --rpc --rpcaddr 192.168.0.8 --rpcport 8989 --port 3000 console
```


我们由于已经创世成功所以第二次进入客户端时我们不用再次指定genesis.json文件路径，而是直接–datadir 指明私链数据路径即可。 
console命令用来开启geth的命令行。
点击回车后，客户端会先经过一阵初始化。在命令提示符出现后，说明已经进入geth console 

![](./_image/2018-04-16-16-34-37.jpg)


## 部署合约
```
pragma solidity ^0.4.4;
contract test {

 function multiply(uint a) returns(uint d){

 return a * 7;
 }

}
```
### 获取智能合约字节码和abi
代码拷贝到https://remix.ethereum.org，编译，然后拷贝字节码和ABI。
字节码
```
6060604052341561000f57600080fd5b5b60ab8061001e6000396000f30060606040526000357c0100
000000000000000000000000000000000000000000000000000000900463ffffffff168063c6888fa1
14603d575b600080fd5b3415604757600080fd5b605b60048080359060200190919050506071565b60
40518082815260200191505060405180910390f35b60006007820290505b9190505600a165627a7a72
30582067d7c851e14e862886b6f53dad6825135557fb3a4b691350c94ea5b80605f6770029
```
ABI
```
[
 {
 "constant": true,
 "inputs": [
 {
 "name": "a",
 "type": "uint256"
 }
 ],
 "name": "multiply",
 "outputs": [
 {
 "name": "d",
 "type": "uint256"
 }
 ],
 "payable": false,
 "type": "function",
 "stateMutability": "view"
 }
]
```
在bejson中转义成字符串
http://www.bejson.com
```
[{\"constant\":true,\"inputs\":[{\"name\":\"a\",\"type\":\"uint256\"}],\"name\":\"
multiply\",\"outputs\":[{\"name\":\"d\",\"type\":\"uint256\"}],\"payable\":false,\
"type\":\"function\",\"stateMutability\":\"view\"}]
```
通过abi创建合约对象
```
> var abi = JSON.parse('[{\"constant\":true,\"inputs\":[{\"name\":\"a\",\"type\":\
"uint256\"}],\"name\":\"multiply\",\"outputs\":[{\"name\":\"d\",\"type\":\"uint256
\"}],\"payable\":false,\"type\":\"function\",\"stateMutability\":\"view\"}]')
> myContract = web3.eth.contract(abi)
{
 abi: [{
 constant: false,
 inputs: [{...}],
 name: "multiply",
 outputs: [{...}],
 payable: false,
 type: "function"
 }],
 eth: {
 accounts: ["0x2abf46d8b0d940cdeedd55872bc0648add40227d"],
 blockNumber: 384,
 coinbase: "0x2abf46d8b0d940cdeedd55872bc0648add40227d",
 compile: {
 lll: function(),
 serpent: function(),
 solidity: function()
 },
 defaultAccount: undefined,
 defaultBlock: "latest",
 gasPrice: 0,
 hashrate: 0,
 mining: false,
 pendingTransactions: [],
 protocolVersion: "0x3f",
 syncing: false,
 call: function(),
 contract: function(abi),
 estimateGas: function(),
 filter: function(fil, callback),
 getAccounts: function(callback),
 getBalance: function(),
 getBlock: function(),
 getBlockNumber: function(callback),
 getBlockTransactionCount: function(),
 getBlockUncleCount: function(),
 getCode: function(),
 getCoinbase: function(callback),
 getCompilers: function(),
 getGasPrice: function(callback),
 getHashrate: function(callback),
 getMining: function(callback),
 getPendingTransactions: function(callback),
 getProtocolVersion: function(callback),
 getRawTransaction: function(),
 getRawTransactionFromBlock: function(),
 getStorageAt: function(),
 getSyncing: function(callback),
 getTransaction: function(),
 getTransactionCount: function(),
 getTransactionFromBlock: function(),
 getTransactionReceipt: function(),
 getUncle: function(),
 getWork: function(),
 iban: function(iban),
 icapNamereg: function(),
 isSyncing: function(callback),
 namereg: function(),
 resend: function(),
 sendIBANTransaction: function(),
 sendRawTransaction: function(),
 sendTransaction: function(),
 sign: function(),
 signTransaction: function(),
 submitTransaction: function(),
 submitWork: function()
 },
 at: function(address, callback),
 getData: function(),
 new: function()
}
```
预估手续费
```
> bytecode = "6060604052341561000f57600080fd5b5b60ab8061001e6000396000f30060606040
526000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff
168063c6888fa114603d575b600080fd5b3415604757600080fd5b605b600480803590602001909190
50506071565b6040518082815260200191505060405180910390f35b60006007820290505b91905056
00a165627a7a7230582067d7c851e14e862886b6f53dad6825135557fb3a4b691350c94ea5b80605f6
770029"
"6060604052341561000f57600080fd5b5b60ab8061001e6000396000f30060606040526000357c010
0000000000000000000000000000000000000000000000000000000900463ffffffff168063c6888fa
114603d575b600080fd5b3415604757600080fd5b605b60048080359060200190919050506071565b6
040518082815260200191505060405180910390f35b60006007820290505b9190505600a165627a7a7
230582067d7c851e14e862886b6f53dad6825135557fb3a4b691350c94ea5b80605f6770029"
> web3.eth.estimateGas({data: bytecode})
Error: invalid argument 0: json: cannot unmarshal hex string without 0x prefix int
o Go struct field CallArgs.data of type hexutil.Bytes
 at web3.js:3104:20
 at web3.js:6191:15
 at web3.js:5004:36
 at <anonymous>:1:1
> bytecode = "0x6060604052341561000f57600080fd5b5b60ab8061001e6000396000f300606060
40526000357c0100000000000000000000000000000000000000000000000000000000900463ffffff
ff168063c6888fa114603d575b600080fd5b3415604757600080fd5b605b6004808035906020019091
9050506071565b6040518082815260200191505060405180910390f35b60006007820290505b919050
5600a165627a7a7230582067d7c851e14e862886b6f53dad6825135557fb3a4b691350c94ea5b80605
f6770029"
"0x6060604052341561000f57600080fd5b5b60ab8061001e6000396000f30060606040526000357c0
100000000000000000000000000000000000000000000000000000000900463ffffffff168063c6888
fa114603d575b600080fd5b3415604757600080fd5b605b60048080359060200190919050506071565
b6040518082815260200191505060405180910390f35b60006007820290505b9190505600a165627a7
a7230582067d7c851e14e862886b6f53dad6825135557fb3a4b691350c94ea5b80605f6770029"
> web3.eth.estimateGas({data: bytecode})
98391
> 
```
备注：字节码前面需要添加 0x 。手续费大概为 98391 gas 。



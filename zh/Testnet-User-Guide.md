# ALVEY 区块链测试网络使用指南

## 1. 下载安装主网钱包
请参考 [《如何部署Alvey量子链节点》](./Guidance-of-Alvey-Deployment-and-RPC-Settings.md) 中“获取Alvey节点”的部分。

## 2. 启动测试网络
使用命令 `./alveyd --testnet` 或者 `./alvey-qt --testnet` 启动程序即可进入测试网络模式。

## 3. 获取测试币
访问 [测试网络水龙头](http://testnet-faucet.alveyscan.com/#!/)，输入钱包地址领取测试币。

## 4. 测试网络浏览器
[https://testnet.alveychain.com/](https://testnet.alveychain.com/)  
[https://testnet.alveyscan.com/](https://testnet.alveyscan.com/)

## 5. 回归测试模式
使用命令 `./alveyd --regtest ` 或者 `./alvey-qt --regtest` 启动程序即可进入回归测试模式。

通过运行 `./alvey-cli --regtest generate [num]` 可以生成新的区块。






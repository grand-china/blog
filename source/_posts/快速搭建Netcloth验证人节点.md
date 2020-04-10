---
title: 快速搭建 NetCloth 验证人节点
date: 2020-04-10 18:40:30
tags: NetCloth
---

## NetCloth 公测链 快速搭建：创建验证人

### 前言：
NetCloth是一个生态系统，分为底层链和应用两部分。
NetCloth 区块链是一个点对点的分布式系统。用于记录网络状态，同时为生态内应用提供底层支持。
NetCloth APP是一个致力于个人掌握数据与数据安全交换的安全社交软件，是其应用的重要组成部分。

现在 NetCloth 区块链开始公测了：https://www.netcloth.org/#/starfish

### 一、选购腾讯云服务器
新用户可点击：  https://url.cn/51xsiTM 获取优惠
1、服务器系统配置：Ubuntu 18.04


### 二、准备开发环境
本地ssh、或者web ssh终端登录到 home 目录：

1、 安装依赖
```
ubuntu:~$ sudo apt-get update
ubuntu:~$ sudo apt-get install git gcc cmake make golang-statik
```

2、配置go
```
ubuntu:~$ wget https://dl.google.com/go/go1.13.5.linux-amd64.tar.gz
ubuntu:~$ tar -xvf go1.13.5.linux-amd64.tar.gz
ubuntu:~$ sudo mv go /usr/local
```
3、注意在VIM 中修改~/.bashrc，添加配置变量：
```
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
export GO111MODULE=on
```
4、修改完之后执行：
```
ubuntu:~$ source ~/.bashrc
```
5、下载链源码
```
ubuntu:~$ git clone https://github.com/netcloth/netcloth-chain.git
ubuntu:~$ cd netcloth-chain && git checkout testnet-v1.1.0
```
6、Note：大陆腾讯云服务器配置goproxy，不然可能go下载不下来
```
ubuntu:~$ export GOPROXY=https://goproxy.io
```
7、编译
```
ubuntu:~$ make install
```
到此，NetCloth 区块链已经搭建准备完毕！喝口水放松下😄


### 三、加入测试网

1、初始化节点配置，侧链名
nchd init #mark# --chain-id nch-testnet
将 #mark#  替换成 自定义名字 your_custom_name
eg：nchd init netcloth --chain-id nch-testnet
```
ubuntu:~$ nchd init your_custom_name  --chain-id nch-testnet
```
2.下载测试网genesis文件：
```
ubuntu:~$ wget http://nch.oss-cn-hangzhou.aliyuncs.com/pkgs/genesis.json -O  ~/.nchd/config/genesis.json
```
3、修改配置文件. Note: 是修改，不是添加追加！！
```
ubuntu:~$ vim ~/.nchd/config/config.toml

在[p2p]配置部分，修改seeds和persistent_peers配置项
# Comma separated list of seed nodes to connect to
seeds = "8ba581e85a00337147ffbf22bc13da78640568b3@13.58.188.155:26656,c57df7491a235753439fa3ea7d908f0ec42e8670@18.191.12.61:26656,b726519a738239378dbb15520f493d1a9a355593@13.124.101.63:26656"


# Comma separated list of nodes to keep persistent connections to
persistent_peers = "8ba581e85a00337147ffbf22bc13da78640568b3@13.58.188.155:26656,c57df7491a235753439fa3ea7d908f0ec42e8670@18.191.12.61:26656,b726519a738239378dbb15520f493d1a9a355593@13.124.101.63:26656"

```



4、后台启动节点
```
ubuntu:~$ nohup nchd start --trace 1>nchd.out 2>&1 &
ubuntu:~$ nohup nchcli rest-server 1>cli.out 2>&1 &
```
5、一段时间后，查看节点同步状态
```
ubuntu:~$ curl http://127.0.0.1:26657/status
```
输出 包含 如下
"sync_info": {  //当前节点信息
      "latest_block_hash": "A4E5D60DE7CFB6598846A4131302C8FD28F2697DF2291B33B0892A9EACB562D8", // 最新的区块 hash
      "latest_app_hash": "32F0B29280EDF3BEAE98424D9AA256EDBEFC973D1C33431A8D74FCA3BC3B6582",
      "latest_block_height": "1489",     // 当前节点同步到的最新区块高度 //最新区块高度
      "latest_block_time": "2019-09-10T05:33:13.428333584Z", //最新区块时间 
      "catching_up": false ### 是否还在同步中
    },

等最新高度 latest_block_height  和 区块链浏览器节点一致，就说明同步完成。
浏览器：https://explorer.netcloth.org/


### 四、添加运行验证人

1、配置环境
```
ubuntu:~$ nchcli config chain-id nch-testnet
ubuntu:~$ nchcli config output json
ubuntu:~$ nchcli config indent true
ubuntu:~$ nchcli config trust-node true
```
2、添加账户
nchcli keys add #mark#
eg：nchcli keys add super
将 #mark#  替换成 自定义账户名 your_moniker_name
会显示在在区块链浏览器Moniker字段上
```
ubuntu:~$ nchcli keys add your_moniker_name
```
Note: 按照提示输入加密账号用的密码(后续执行各种交易都需要用该密码)，将命令返回的信息谨慎保存


3、查看账户信息
```
ubuntu:~$ nchcli keys  list
```
输出包含
[
  {
    "name": "super",
    "type": "local",
    "address": "nch1nkw4wxj6wttwm3uergymmr8c2fvspuk9t6jw9r",
    "pubkey": "nchpub1addwnpepqd8nv07rg6h3mm0dwflmx82xh76x74ncd0zuste3uysnl3ak75dmqddmysv"
  }
]


4、获得测试token
```
使用本地浏览器访问 https://docs.netcloth.org/nch/get_token?#address#
将#address# 替换成 上一步获取的 address
```
即：打开本机浏览器，访问 https://docs.netcloth.org/nch/get_token?nch1nkw4wxj6wttwm3uergymmr8c2fvspuk9t6jw9r


5、创建验证人

```
nchcli tx staking create-validator \
  --amount=1000000000000000pnch \
  --pubkey=$(nchd tendermint show-validator -o text) \
  --moniker=your_moniker_name \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="100" \
  --from=$(nchcli keys show -a your_moniker_name) \
  --ip=your_node_ip \
  --gas=200000  
```

your_moniker_name  替换为 2步添加账户时设置的name
your_node_ip 替换为 腾讯云服务器公网IP:  xx.xx.xx.xx
```
ubuntu:~$  替换后的 nchcli tx staking create-validator \
```
note：
1000000000000000pnch = 1000nch
1nch  = 10^12pnch =  1个投票权

👌，验证人添加完毕！ 并且已经启动

#### （可选） 6. 向验证人 抵押 委托
```
ubuntu:~$ nchcli query staking validators 
查询验证人地址，包含自己 your_moniker_name  的节点的  operator_address，就是

nchcli tx staking delegate  #验证人地址#  2000990000000000pnch --from=#your_moniker_name# --gas=200000 --gas-prices=1000.0pnch
```

参考：
1. https://docs.netcloth.org/get-started/how-to-become-validator.html#_1-%E5%AE%89%E8%A3%85%E5%B9%B6%E9%83%A8%E7%BD%B2%E5%85%A8%E8%8A%82%E7%82%B9
2. https://docs.netcloth.org/get-started/how-to-delegate.html#%E6%9F%A5%E8%AF%A2%E9%AA%8C%E8%AF%81%E4%BA%BA%E5%88%97%E8%A1%A8


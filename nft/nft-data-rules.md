## NFT 数据监控和NFT推荐产品设计文档(seek，Barrett, 拉飞, luna, eren)

### 一. 概述

本产品主要是对 NFT 的合约创建，NFT mint 和  NFT 交易的数据进行抓取，做数据统计分析，给用户推荐高价值的 NFT。业务规则包含，巨鲸钱包地址监控，有价值的 NFT 项目监控，高价值的单品 NFT 监控，NFT 价值分析，NFT 稀有度分析等。

### 二. 架构设计

整个项目设计架构图如下：

![nft-data-1.png](https://github.com/savour-labs/savour-docs-chinese/blob/main/images/nft-data-1.png)

#### 1.规则组件详细逻辑

##### 1.1.巨鲸钱包规则

- 巨鲸钱包地址的评判依据规则一：根据 NFT 持有者的 NFT + Token 价值做判断(曾经持有也算)，大于每一个值就算是巨鲸钱包，评断为巨鲸钱包的地址，自动监控，一旦这个钱包的价值小于规定值，自动解除监控，曾经持有的价值和当前 NFT 持有的价值。
- 巨鲸地址交易 NFT 得活跃值
- 巨鲸钱包地址的评判依据规则二：交易所热钱包地址，大户地址，知名圈内投资机构和投资地址直接监控。

判断为巨鲸钱包的地址需要监控所有相关的数据，将清洗过的数据推到数据清洗中心，供给业务中台使用。

##### 1.2.有价值的 NFT 项目判断规则

- NFT 集合的平均价高于某个值
- NFT 集合的平均流转次数大于某个值
- NFT 集合持有地址的数量大于某个值
- NFT 成交均值->价格走势-价格曲线

同时达到以上三个条件的 NFT 项目，需要监控他们 NFT 的 mint 和 NFT 交易情况，以及判断出新开启的 NFT 项目，将清洗过的数据推到数据清洗中心，供给业务中台使用。

##### 1.3.单个有价值的 NFT 判断依据

- NFT 的流转次数大于某个值
- NFT 最近 N 次交易交易金额大于某个值
- NFT 成交均值->价格走势-价格曲线

同时达到以上两个个条件的 NFT 项目，需要监控 NFT 交易情况，将数据推到数据清洗中心，供给业务中台使用。

##### 1.4. 数据逻辑

- 铸造时间
- 首次成交时间+成交价
- 末次成交时间+成交价
- 历史成交价格曲线+时间
- 系列NFT的信息, NFT代表的含义, 品牌故事
- 该NFT的稀缺性
- 社区粉丝数量
- 换手次数
- BTC价格变化曲线: BTC价格变化曲线和NFT的曲线变化结合分析(同一张图表)
- 全市场NFT的交易量+时间
- 全市场NFT的市值+时间

![roireference.jpg](https://github.com/savour-labs/savour-docs-chinese/blob/main/images/roireference.jpg)


### 三.业务细节逻辑

#### 1.规则配置

![nft-data-2.png](https://github.com/savour-labs/savour-docs-chinese/blob/main/images/nft-data-2.png)


```
             调用规则RPC
          --------------->
hailstone                   savour-nft【config规则配置】
          <---------------
               返回
```

               
具体详情参考规则配置 RPC 接口


#### 2.数据清洗

![nft-data-3.png](https://github.com/savour-labs/savour-docs-chinese/blob/main/images/nft-data-3.png)


```
      数据清洗                            获取清洗规则
入库<----------savour-nft【data 中心】 <-------------- savour-nft【config规则配置】

```

#### 3.数拉取服务

![nft-data-4.png](https://github.com/savour-labs/savour-docs-chinese/blob/main/images/nft-data-4.png)


```              
              巨鲸地址，NFT交易数据等
数据聚合网站------------------------->数据抓取服务
              
              交易数据，合约创建，NFT 转移，NFT holder 等
区块链网络 ----------------------------------------------> 监控服务
```
              
#### 4.干净数据推送

![nft-data-5.png](https://github.com/savour-labs/savour-docs-chinese/blob/main/images/nft-data-5.png)

```

            调用规则RPC
          --------------->
hailstone                   savour-nft【config规则配置】
          <---------------
             返回干净数据
```

具体详情参考规则配置 RPC 接口 

### 附录1-需要爬虫的网站

- Oklink: https://www.oklink.com/docs/en/#rest-api-address-top-100-richest-addresses
- 巨鲸地址抓取网站一：https://dune.com/0xBi/0xBi
- polygonscan: https://polygonscan.com/labelcloud
- arbiscan: https://arbiscan.io/labelcloud
- etherscan: https://etherscan.io/labelcloud


### 附录2
- 钱包查询功能参考：https://pro.nansen.ai/wallet-profiler
- 巨鲸地址判断参考：https://nftgo.io/whale-tracking/list
- 可数据抓取网站链接： https://www.chaineye.info/5/newsletter_detail


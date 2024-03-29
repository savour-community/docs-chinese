# Savour 社交恢复钱包方案细节

### 一. 项目概述

社交恢复钱包是使用门限秘密共享的密码学技术方案对钱包的助记词或私钥进行切片备份管理，当用户的助记词或私钥丢失，可以发起社交恢复的方式进行助记词和私钥的恢复，我们使用的是云端抗干扰的秘密共享方案。在社交恢复钱包中，我们将把助记词和私钥屏蔽，不在需要用户自己去备份助记词和私钥，而是通过社交的方式进行助记词和私钥的备份。

### 二. 项目架构
Savour wallet 社交恢复的钱包的方案设计图如下：

![s1.png](https://github.com/savour-labs/savour-docs-chinese/blob/main/images/s1.png)


在我们savour wallet 中，我们整个社交恢复的流程如下：
1. 用户的私钥或者助记词和一个大随机数做一次异或算法，得到一个新的秘密值，我们把大随机数叫做 head, 秘密值叫做 body，head 加密之后上传到 savour wallet 云端。
2. 将 body 做门限共享秘密算法，拆分成 N 份 shadow,  设置 K 份可以恢复 body
3. shadow-1 加密之后存储在本地，使用 AES 加密，密码Hash之后做为加密 Key，
4. shadow-2 加密之后存储到savour wallet 云端，使用 AES 加密，密码和设备 ID 做为加密 Key
5. shadow-3 ... n 加密之后存储到密钥柜和社交圈的好友
6. 助记词或者私钥丢失发起恢复，获取密钥柜里面或者好友处的 k - 2 份私钥，再获取云端的 shadow-2 和 header。
7. 逆门限共享秘密算法通过 K 份 shadow 恢复出 body
8. body 和 head 做逆异或算法得到助记词和私钥。
关于密钥柜：
密码柜是 savour 官方提供的一个密钥托管服务，用户可以自行运行密钥柜服务存储自己的密钥 shadow，也可以将密钥 shadow 加密之后保存到我们官方密钥柜，密钥柜对接的是各大区块链存储平台，我们将把密钥进行深层次加密之后把密钥上传到各大区块链存储平台，即使有一天密钥柜不运行了，用户也可以从各大区块链存储平台获取到自己加密的密钥分片，根据加密规则进行解密就可以得到明文的密钥 shadown
关于社交圈：
未来 savour wallet 会上线隐私社交，除了平台，密钥柜，用户好友，用户还可以通过隐私社交的方式备份自己的密钥 shadow

### 三. 总结

社交恢复钱包最早出现是以以太坊的智能合约方式实现的，仅仅只支持以太坊生态，而我们 Savour 社交恢复钱包是使用密码学的技术解决方案，可以将社交恢复钱包推广到所有区块链生态。这是也是我们 Savour 社交恢复钱包和其他社交恢复钱包相比的核心竞争力。
 
 
### 四. 附

#### 1. 前端实现逻辑


```
const savoursecret = require("savour-secret")

const key = savoursecret.random(512)
console.log("key=", key)

// 将 Key 拆分为 head 和 body
const head_body = savoursecret.share(key, 2, 2)
console.log("head_body=", head_body)


// body 继续拆分
const body = head_body[1]
const shares = savoursecret.share(body, 10, 5)
console.log("shares=", shares)


// body 恢复
const body_recover = savoursecret.combine(shares.slice(4, 9))
console.log("body_recover=", body_recover)


// key 恢复
const shares_recover = [body_recover, head_body[0]]
const key_recover = savoursecret.combine(shares_recover)
console.log("key_recover=", key_recover)
```

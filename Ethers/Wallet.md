# Wallet Class
Wallet 是 [Singer](Singer.md) 类的具体实现。它简化了与以太坊区块链的交互。它直接访问私钥管理，并适用于应用程序内部使用。

## 创建Wallet实例
### 随机生成
```js
const wallet = ethers.Wallet.createRandom()；
```

#### 通过私钥导入
```js
const privateKey = process.env.PRIVATE_KEY; 
const wallet = new ethers.Wallet(privateKey, provider)；
```

#### 助记词导入
```js
const wallet = ethers.Wallet.fromPhrase(mnemonic.phrase)；
```

#### JSON导入
如果用户有来自钱包应用的加密钱包 JSON 文件，则可以使用以下方法解密账户信息
```js
const wallet = await ethers.Wallet.fromEncryptedJson(json, password);
```

## Wallet属性
### `getAddress()`
获取钱包地址

### `mnemonic.phrase`
获取助记词
### `privateKey`
获取私钥

### `getTransactionCount()`
获取链上交易次数
```js
    const txCount1 = await provider.getTransactionCount(wallet1WithProvider)
    const txCount2 = await provider.getTransactionCount(wallet2)
    console.log(`钱包1发送交易次数: ${txCount1}`)
    console.log(`钱包2发送交易次数: ${txCount2}`)

```
---
title: Wallet TON API Reference
description: Complete API documentation for @tetherto/wdk-wallet-ton
author: Raquel Carrasco Gonzalez
lastReviewed: 2025-06-26
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
  metadata:
    visible: false
---

## API Reference

### Table of Contents

| Class | Description | Methods |
|-------|-------------|---------|
| [WalletManagerTon](#walletmanagerton) | Main class for managing TON wallets | [Constructor](#constructor), [Methods](#methods) |
| [WalletAccountTon](#walletaccountton) | Individual TON wallet account implementation | [Constructor](#constructor-1), [Methods](#methods-1) |
| [WalletAccountReadOnlyTon](#walletaccountreadonlyton) | Read-only TON wallet account | [Constructor](#constructor-2), [Methods](#methods-2) |



### WalletManagerTon

The main class for managing TON wallets.  
Extends `WalletManager` from `@tetherto/wdk-wallet`.

#### Constructor

```javascript
new WalletManagerTon(seed, config)
```

**Parameters:**
- `seed` (string | Uint8Array): BIP-39 mnemonic seed phrase or seed bytes
- `config` (object): Configuration object
  - `tonClient` (object | TonClient): TON client configuration or instance
    - `url` (string): TON Center API URL (e.g., 'https://toncenter.com/api/v3')
    - `secretKey` (string, optional): API key for TON Center
  - `transferMaxFee` (number, optional): Maximum fee amount for transfer operations (in nanotons)


**Example:**
```javascript
const wallet = new WalletManagerTon(seedPhrase, {
  tonClient: {
    url: 'https://toncenter.com/api/v3',
    secretKey: 'your-api-key'
  },
  transferMaxFee: 1000000000 // Maximum fee in nanotons (1 TON)
})
```

#### Methods


| Method | Description | Returns |
|--------|-------------|---------|
| `getAccount(index)` | Returns a wallet account at the specified index | `Promise<WalletAccountTon>` |
| `getAccountByPath(path)` | Returns a wallet account at the specified BIP-44 derivation path | `Promise<WalletAccountTon>` |
| `getFeeRates()` | Returns current fee rates for transactions | `Promise<{normal: number, fast: number}>` |
| `dispose()` | Disposes all wallet accounts, clearing private keys from memory | `void` |


##### `getAccount(index)`
Returns a wallet account at the specified index.

**Parameters:**
- `index` (number, optional): The index of the account to get (default: 0)

**Returns:** `Promise<WalletAccountTon>` - The wallet account

**Example:**
```javascript
const account = await wallet.getAccount(0)
```

##### `getAccountByPath(path)`
Returns a wallet account at the specified BIP-44 derivation path.

**Parameters:**
- `path` (string): The derivation path (e.g., "0'/0/0")

**Returns:** `Promise<WalletAccountTon>` - The wallet account

**Example:**
```javascript
const account = await wallet.getAccountByPath("0'/0/1")
```

##### `getFeeRates()`
Returns current fee rates for normal and fast transactions.

**Returns:** `Promise<FeeRates>` - Object containing normal and fast fee rates

**Example:**
```javascript
const feeRates = await wallet.getFeeRates()
console.log('Normal fee rate:', feeRates.normal, 'nanotons')
console.log('Fast fee rate:', feeRates.fast, 'nanotons')
```

##### `dispose()`
Disposes all wallet accounts, clearing private keys from memory.

**Example:**
```javascript
wallet.dispose()
```

#### Properties

##### `seed`
The wallet's seed phrase.

**Type:** `string | Uint8Array`

**Example:**
```javascript
console.log('Seed phrase:', wallet.seed)
```

### WalletAccountTon

Individual TON wallet account implementation. Extends `WalletAccountReadOnlyTon` and implements `IWalletAccount`.


#### Constructor

```javascript
new WalletAccountTon(seed, path, config)
```

**Parameters:**
- `seed` (string | Uint8Array): BIP-39 mnemonic seed phrase or seed bytes
- `path` (string): BIP-44 derivation path (e.g., "0'/0/0")
- `config` (object): Configuration object
  - `tonClient` (object | TonClient): TON client configuration or instance
    - `url` (string): TON Center API URL
    - `secretKey` (string, optional): API key for TON Center
  - `transferMaxFee` (number, optional): Maximum fee amount for transfer operations


**Example:**
```javascript
const account = new WalletAccountTon(seedPhrase, "0'/0/0", {
  tonClient: {
    url: 'https://toncenter.com/api/v3',
    secretKey: 'your-api-key'
  },,
  transferMaxFee: 10000000 // Maximum fee in nanotons (e.g., 0.01 TON)
})
```

#### Methods

| Method | Description | Returns |
|--------|-------------|---------|
| `getAddress()` | Returns the account's TON address | `Promise<string>` |
| `sign(message)` | Signs a message using the account's private key | `Promise<string>` |
| `verify(message, signature)` | Verifies a message signature | `Promise<boolean>` |
| `sendTransaction(tx)` | Sends a TON transaction | `Promise<{hash: string, fee: number}>` |
| `quoteSendTransaction(tx)` | Estimates the fee for a TON transaction | `Promise<{fee: number}>` |
| `transfer(options)` | Transfers Jetton tokens to another address | `Promise<{hash: string, fee: number}>` |
| `quoteTransfer(options)` | Estimates the fee for a Jetton transfer | `Promise<{fee: number}>` |
| `getBalance()` | Returns the native TON balance (in nanotons) | `Promise<number>` |
| `getTokenBalance(tokenAddress)` | Returns the balance of a specific Jetton token | `Promise<number>` |
| `dispose()` | Disposes the wallet account, clearing private keys from memory | `void` |

##### `getAddress()`
Returns the account's address.

**Returns:** `Promise<string>` - The account's TON address

**Example:**
```javascript
const address = await account.getAddress()
console.log('Account address:', address)
```

##### `sign(message)`
Signs a message using the account's private key.

**Parameters:**
- `message` (string): The message to sign

**Returns:** `Promise<string>` - The message signature

**Example:**
```javascript
const signature = await account.sign('Hello, World!')
console.log('Signature:', signature)
```

##### `verify(message, signature)`
Verifies a message signature.

**Parameters:**
- `message` (string): The original message
- `signature` (string): The signature to verify

**Returns:** `Promise<boolean>` - True if the signature is valid

**Example:**
```javascript
const isValid = await account.verify('Hello, World!', signature)
console.log('Signature valid:', isValid)
```

##### `sendTransaction(tx)`
Sends a TON transaction and returns the result with hash and fee.

**Parameters:**
- `tx` (object): The transaction object
  - `to` (string): Recipient TON address (e.g., 'EQ...')
  - `value` (number): Amount in nanotons (1 TON = 1,000,000,000 nanotons)
  - `bounceable` (boolean, optional): Whether the address is bounceable (TON-specific, optional)


**Returns:** `Promise<{hash: string, fee: number}>` - Object containing hash and fee (in nanotons)

**Example:**
```javascript
const result = await account.sendTransaction({
  to: 'EQ...', // TON address
  value: 1000000000 // 1 TON in nanotons
});
console.log('Transaction hash:', result.hash);
console.log('Transaction fee:', result.fee, 'nanotons');
```

##### `quoteSendTransaction(tx)`
Estimates the fee for a transaction.

**Parameters:**
- `tx` (object): The transaction object (same as sendTransaction)
  - `to` (string): Recipient TON address (e.g., 'EQ...')
  - `value` (number): Amount in nanotons (1 TON = 1,000,000,000 nanotons)
  - `bounceable` (boolean, optional): Whether the address is bounceable (TON-specific, optional)

**Returns:** `Promise<{fee: number}>` - Object containing fee estimate (in nanotons)

**Example:**
```javascript
const quote = await account.quoteSendTransaction({
  to: 'EQ...', // TON address
  value: 1000000000 // 1 TON in nanotons
});
console.log('Estimated fee:', quote.fee, 'nanotons');
```

##### `transfer(options)`
Transfers Jettons (TON tokens) to another address.

**Parameters:**
- `options` (object): Transfer options
  - `token` (string): Jetton master contract address (TON format, e.g., 'EQ...')
  - `recipient` (string): Recipient TON address (e.g., 'EQ...')
  - `amount` (number): Amount in Jetton's base units

**Returns:** `Promise<{hash: string, fee: number}>` - Object containing hash and fee (in nanotons)

**Example:**
```javascript
const result = await account.transfer({
  token: 'EQ...',      // Jetton master contract address
  recipient: 'EQ...',  // Recipient's TON address
  amount: 1000000000    // Amount in Jetton's base units
});
console.log('Transfer hash:', result.hash);
console.log('Transfer fee:', result.fee, 'nanotons');
```

##### `quoteTransfer(options)`
Estimates the fee for a Jetton (TON token) transfer.

**Parameters:**
- `options` (object): Transfer options (same as transfer)
  - `token` (string): Jetton master contract address (TON format, e.g., 'EQ...')
  - `recipient` (string): Recipient TON address (e.g., 'EQ...')
  - `amount` (number): Amount in Jetton's base units

**Returns:** `Promise<{fee: number}>` - Object containing fee estimate (in nanotons)

**Example:**
```javascript
const quote = await account.quoteTransfer({
  token: 'EQ...',      // Jetton master contract address
  recipient: 'EQ...',  // Recipient's TON address
  amount: 1000000000    // Amount in Jetton's base units
});
console.log('Transfer fee estimate:', quote.fee, 'nanotons');
```

##### `getBalance()`
Returns the native TON balance (in nanotons).

**Returns:** `Promise<number>` - Balance in nanotons

**Example:**
```javascript
const balance = await account.getBalance();
console.log('Balance:', balance, 'nanotons');
```

##### `getTokenBalance(tokenAddress)`
Returns the balance of a specific Jetton (TON token).

**Parameters:**
- `tokenAddress` (string): The Jetton master contract address (TON format, e.g., 'EQ...')

**Returns:** `Promise<number>` - Token balance in base units 

**Example:**
```javascript
const tokenBalance = await account.getTokenBalance('EQ...');
console.log('Token balance:', tokenBalance, 'nanotons');
```

##### `dispose()`
Disposes the wallet account, clearing private keys from memory.

**Example:**
```javascript
account.dispose()
```

#### Properties

| Property | Type | Description |
|----------|------|-------------|
| `index` | `number` | The derivation path's index of this account |
| `path` | `string` | The full derivation path of this account |
| `keyPair` | `{publicKey: Buffer, privateKey: Buffer}` | The account's public and private key pair as buffers |

**Example:**
```javascript
const { publicKey, privateKey } = account.keyPair
console.log('Public key length:', publicKey.length)
console.log('Private key length:', privateKey.length)
```

## Types

### TonTransaction

```typescript
interface TonTransaction {
  /**
   * Recipient's TON address in base64 format
   * @example 'EQD4FPq...'
   */
  to: string;

  /**
   * Amount to send in nanotons (1 TON = 1,000,000,000 nanotons)
   * @example 1000000000 // 1 TON
   */
  value: number;

  /**
   * Whether the destination address is bounceable
   * @default true
   */
  bounceable?: boolean;

  /**
   * Optional message payload
   */
  payload?: string;

  /**
   * Optional contract initialization state
   */
  stateInit?: Cell | null;
}
```

### TransferOptions

```typescript
interface TransferOptions {
  /**
   * Jetton master contract address
   * @example 'EQD4FPq...'
   */
  token: string;

  /**
   * Recipient's TON address
   * @example 'EQD4FPq...'
   */
  recipient: string;

  /**
   * Amount in Jetton's base units
   * @example 1000000000 // Amount depends on token decimals
   */
  amount: number;
}
```

### TransactionResult

```typescript
interface TransactionResult {
  /**
   * Transaction hash in base64 format
   * @example 'EQD4FPq...'
   */
  hash: string;

  /**
   * Transaction fee in nanotons
   * @example 100000 // 0.0001 TON
   */
  fee: number;
}
```

### FeeRates

```typescript
interface FeeRates {
  /**
   * Fee rate for normal priority transactions (in nanotons)
   * @example 100000000 // 0.1 TON
   */
  normal: number;

  /**
   * Fee rate for high priority transactions (in nanotons)
   * @example 200000000 // 0.2 TON
   */
  fast: number;
}
```

### KeyPair

```typescript
interface KeyPair {
  /**
   * Ed25519 public key
   */
  publicKey: Buffer;

  /**
   * Ed25519 private key (sensitive data)
   * @security Never expose or log this value
   */
  privateKey: Buffer;
}
```

### TonWalletConfig

```typescript
interface TonWalletConfig {
  /**
   * TON Center client configuration
   */
  tonClient?: {
    /**
     * TON Center API endpoint
     * @example 'https://toncenter.com/api/v3'
     */
    url: string;

    /**
     * Optional API key for higher rate limits
     */
    secretKey?: string;
  };

  /**
   * Maximum allowed fee for transfers (in nanotons)
   * @example 1000000000 // 1 TON
   */
  transferMaxFee?: number;
}
```



<table data-card-size="large" data-view="cards">
	<thead>
		<tr>
			<th></th>
			<th></th>
			<th></th>
			<th data-hidden data-card-target data-type="content-ref"></th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>
				<i class="fa-code">:code:</i>
			</td>
			<td>
				<strong>Node.js Quickstart</strong>
			</td>
			<td>Get started with WDK in a Node.js environment</td>
			<td>
				<a href="../../../start-building/nodejs-bare-quickstart.md">nodejs-quickstart.md</a>
			</td>
		</tr>
    <tr>
			<td>
				<i class="fa-mobile-alt">:mobile-alt:</i>
			</td>
			<td>
				<strong>React Native Quickstart</strong>
			</td>
			<td>Build mobile wallets with React Native Expo</td>
			<td>
				<a href="../../../start-building/react-native-quickstart.md">react-native-quickstart.md</a>
			</td>
		</tr>
    <tr>
			<td>
				<i class="fa-code">:code:</i>
			</td>
			<td>
				<strong>WDK TON Wallet Usage</strong>
			</td>
			<td>Get started with WDK's TON Wallet Usage</td>
			<td>
				<a href="./configuration.md">WDK TON Wallet Usage</a>
			</td>
		</tr>
        <tr>
			<td>
				<i class="fa-code">:code:</i>
			</td>
			<td>
				<strong>WDK TON Wallet Configuration</strong>
			</td>
			<td>Get started with WDK's TON Wallet Configuration</td>
			<td>
				<a href="./configuration.md">WDK TON Wallet Configuration</a>
			</td>
		</tr>
	</tbody>
</table>

***

### Need Help?

{% include "../../../.gitbook/includes/support-cards.md" %}


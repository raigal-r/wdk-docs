---
title: Wallet EVM ERC-4337 Guides
description: Installation, quick start, and usage examples for @tetherto/wdk-wallet-evm-erc-4337
lastReviewed: 2025-06-26
icon: book-open
---

## Installation

To install the `@tetherto/wdk-wallet-evm-erc-4337` package, follow these instructions:

### Public Release

Once the package is publicly available, you can install it using npm:

```bash
npm install @tetherto/wdk-wallet-evm-erc-4337
```

### Private Access

If you have access to the private repository, install the package from the develop branch on GitHub:

```bash
npm install git+https://github.com/tetherto/wdk-wallet-evm-erc-4337.git#develop
```

After installation, ensure your package.json includes the dependency correctly:

```json
"dependencies": {
  // ... other dependencies ...
  "@tetherto/wdk-wallet-evm-erc-4337": "git+ssh://git@github.com:tetherto/wdk-wallet-evm-erc-4337.git#develop"
  // ... other dependencies ...
}
```

## Quick Start

### Importing from `@tetherto/wdk-wallet-evm-erc-4337`

1. WalletManagerEvmErc4337: Main class for managing ERC-4337 wallets
2. WalletAccountEvmErc4337: Use this for full access accounts
3. WalletAccountReadOnlyEvmErc4337: Use this for read-only accounts

### Creating a New Wallet

```javascript
import WalletManagerEvmErc4337, { 
  WalletAccountEvmErc4337, 
  WalletAccountReadOnlyEvmErc4337 
} from '@tetherto/wdk-wallet-evm-erc-4337'

// Use a BIP-39 seed phrase (replace with your own secure phrase)
const seedPhrase = 'your twelve word seed phrase here' // Replace with actual seed generation


// Create wallet manager with ERC-4337 configuration
const wallet = new WalletManagerEvmErc4337(seedPhrase, {
  chainId: 1, // Ethereum mainnet
  provider: 'https://rpc.mevblocker.io/fast',
  bundlerUrl: 'https://api.candide.dev/public/v3/ethereum',
  paymasterUrl: 'https://api.candide.dev/public/v3/ethereum',
  paymasterAddress: '0x8b1f6cb5d062aa2ce8d581942bbb960420d875ba',
  entryPointAddress: '0x0000000071727De22E5E9d8BAf0edAc6f37da032',
  safeModulesVersion: '1.0.0',
  paymasterToken: {
    address: '0xdAC17F958D2ee523a2206206994597C13D831ec7'
  },
  
  // Optional parameter
  transferMaxFee: 100000000000000 // Optional: Maximum fee amount for transfer operations (in wei)

})

// Create a read-only account
const readOnlyAccount = new WalletAccountReadOnlyEvmErc4337('0x...', { // Smart contract wallet address
  chainId: 1, // Required: Blockchain ID
  provider: 'https://rpc.mevblocker.io/fast', // Required: RPC provider
  bundlerUrl: 'https://api.candide.dev/public/v3/ethereum', // Required: Bundler service
  paymasterUrl: 'https://api.candide.dev/public/v3/ethereum', // Required: Paymaster service
  paymasterAddress: '0x8b1f6cb5d062aa2ce8d581942bbb960420d875ba', // Required: Paymaster contract
  entryPointAddress: '0x0000000071727De22E5E9d8BAf0edAc6f37da032', // Required: EntryPoint contract
  safeModulesVersion: '1.0.0', // Required: Safe modules version
  paymasterToken: { // Required: Paymaster token configuration
    address: '0xdAC17F958D2ee523a2206206994597C13D831ec7' // USDT token address
  }
})
```

### Managing Multiple Accounts

```javascript
import WalletManagerEvmErc4337 from '@tetherto/wdk-wallet-evm-erc-4337'

// Get the first account (index 0)
const account = await wallet.getAccount(0)
const address = await account.getAddress()
console.log('Account 0 address:', address)

// Get the second account (index 1)
const account1 = await wallet.getAccount(1)
const address1 = await account1.getAddress()
console.log('Account 1 address:', address1)

// Get account by custom derivation path
const customAccount = await wallet.getAccountByPath("0'/0/5")
const customAddress = await customAccount.getAddress()
console.log('Custom account address:', customAddress)
```

### Checking Balances

#### Owned Account

For accounts where you have the seed phrase and full access:

```javascript
import WalletManagerEvmErc4337 from '@tetherto/wdk-wallet-evm-erc-4337'

// Assume wallet and account are already created
// Get native token balance (in wei)
const balance = await account.getBalance()
console.log('Native balance:', balance, 'wei') // 1 ETH = 1000000000000000000 wei

// Get ERC20 token balance
const tokenContract = '0xdAC17F958D2ee523a2206206994597C13D831ec7'; // USDT contract address
const tokenBalance = await account.getTokenBalance(tokenContract);
console.log('USDT balance:', tokenBalance);

// Get paymaster token balance (for paying fees)
const paymasterBalance = await account.getPaymasterTokenBalance()
console.log('Paymaster token balance:', paymasterBalance, 'units')

// Note: Provider is required for balance checks
// Make sure wallet was created with a provider configuration
```

#### Read-Only Account

For addresses where you don't have the seed phrase:

```javascript
import { WalletAccountReadOnlyEvmErc4337 } from '@tetherto/wdk-wallet-evm-erc-4337'

// Create a read-only account with complete ERC-4337 configuration
const readOnlyAccount = new WalletAccountReadOnlyEvmErc4337('0x...', { // Smart contract wallet address
  chainId: 1, // Blockchain ID
  provider: 'https://rpc.mevblocker.io/fast',
  bundlerUrl: 'https://api.candide.dev/public/v3/ethereum',
  paymasterUrl: 'https://api.candide.dev/public/v3/ethereum',
  paymasterAddress: '0x8b1f6cb5d062aa2ce8d581942bbb960420d875ba',
  entryPointAddress: '0x0000000071727De22E5E9d8BAf0edAc6f37da032',
  safeModulesVersion: '1.0.0',
  paymasterToken: {
    address: '0xdAC17F958D2ee523a2206206994597C13D831ec7' // USDT
  }
})

// Check native token balance
const balance = await readOnlyAccount.getBalance()
console.log('Native balance:', balance, 'wei')


// Check ERC20 token balance
const tokenBalance = await readOnlyAccount.getTokenBalance('0xdAC17F958D2ee523a2206206994597C13D831ec7')
console.log('USDT balance:', tokenBalance)

// Check paymaster token balance (uses the configured paymaster token)
const paymasterBalance = await readOnlyAccount.getPaymasterTokenBalance()
console.log('Paymaster token balance:', paymasterBalance, 'units')

// Note: ERC20 balance checks use the standard balanceOf(address) function
// Make sure the contract address is correct and implements the ERC20 standard
```

### Sending Gasless Transactions

```javascript
// Send a gasless transaction (fees paid in paymaster token)
const result = await account.sendTransaction({
  to: '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6',
  value: 1000000000000000000, // 1 ETH in wei (use number, not bigint)
  data: '0x' // Optional transaction data
})
console.log('Transaction hash:', result.hash)
console.log('Transaction fee (in paymaster token units):', result.fee)

// Send transaction with custom paymaster token
const customResult = await account.sendTransaction({
  to: '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6',
  value: 1000000000000000000
}, {
  paymasterToken: {
    address: '0x...' // Override default paymaster token
  }
})

// Get transaction fee estimate
const quote = await account.quoteSendTransaction({
  to: '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6',
  value: 1000000000000000000
})
console.log('Estimated fee (in paymaster token units):', quote.fee)
```

### Token Transfers with Gasless Transactions

```javascript
// Transfer ERC20 tokens using gasless transactions
const transferResult = await account.transfer({
  token: '0xdAC17F958D2ee523a2206206994597C13D831ec7', // USDT
  recipient: '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6',
  amount: 1000000 // 1 USDT (6 decimals) - use appropriate units for token
})
console.log('Transfer hash:', transferResult.hash)
console.log('Transfer fee (in paymaster token units):', transferResult.fee)

// Transfer with custom configuration
const customTransferResult = await account.transfer({
  token: '0xdAC17F958D2ee523a2206206994597C13D831ec7',
  recipient: '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6',
  amount: 1000000
}, {
  paymasterToken: {
    address: '0x...' // Override paymaster token
  },
  transferMaxFee: 100000 // Maximum fee limit in paymaster token units
})

// Quote token transfer
const transferQuote = await account.quoteTransfer({
  token: '0xdAC17F958D2ee523a2206206994597C13D831ec7',
  recipient: '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6',
  amount: 1000000
})
console.log('Transfer fee estimate (in paymaster token units):', transferQuote.fee)

// Error handling for fee limits
try {
  const result = await account.transfer({
    token: '0x...',
    recipient: '0x...',
    amount: 1000000
  })
} catch (error) {
  if (error.message.includes('Exceeded maximum fee')) {
    console.error('Transfer cancelled: Fee too high')
  } else if (error.message.includes('not enough funds')) {
    console.error('Insufficient paymaster token balance')
  }
}
```

### Memory Management

```javascript
// Dispose wallet accounts to clear private keys from memory
account.dispose()

// Dispose entire wallet manager
wallet.dispose()
```

## Complete Examples

### Complete Wallet Setup

```javascript
import WalletManagerEvmErc4337 from '@tetherto/wdk-wallet-evm-erc-4337'

async function setupErc4337Wallet() {
  // Use a BIP-39 seed phrase (replace with your own secure phrase)
  const seedPhrase = 'abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon about'
  
  // Create ERC-4337 wallet manager
  const wallet = new WalletManagerEvmErc4337(seedPhrase, {
    chainId: 1, // Ethereum mainnet
    provider: 'https://rpc.mevblocker.io/fast',
    bundlerUrl: 'https://api.candide.dev/public/v3/ethereum',
    paymasterUrl: 'https://api.candide.dev/public/v3/ethereum',
    paymasterAddress: '0x8b1f6cb5d062aa2ce8d581942bbb960420d875ba',
    entryPointAddress: '0x0000000071727De22E5E9d8BAf0edAc6f37da032',
    safeModulesVersion: '1.0.0',
    paymasterToken: {
      address: '0xdAC17F958D2ee523a2206206994597C13D831ec7' // USDT
    },
    transferMaxFee: 100000 // Optional: Maximum fee in paymaster token units
  })
  
  // Get first account
  const account = await wallet.getAccount(0)
  const address = await account.getAddress()
  console.log('Safe account address:', address)
  
  // Check balances
  const nativeBalance = await account.getBalance()
  console.log('Native balance:', nativeBalance, 'wei')
  
  const paymasterBalance = await account.getPaymasterTokenBalance()
  console.log('Paymaster token balance:', paymasterBalance, 'USDT units')
  
  return { wallet, account, address, paymasterBalance }
}
```

### Gasless Transaction Example

```javascript
async function sendGaslessTransaction(account) {
  try {
    // Get fee estimate first
    const quote = await account.quoteSendTransaction({
      to: '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6',
      value: 1000000000000000000, // 1 ETH (use number, not bigint)
      data: '0x'
    })
    console.log('Estimated fee:', quote.fee, 'paymaster token units')
    
    // Send ETH without holding any ETH for gas fees
    const result = await account.sendTransaction({
      to: '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6',
      value: 1000000000000000000, // 1 ETH
      data: '0x' // Optional transaction data
    })
    
    console.log('Gasless transaction hash:', result.hash)
    console.log('Fee paid in paymaster token:', result.fee, 'units')
    
    return result
    
  } catch (error) {
    if (error.message.includes('not enough funds')) {
      console.error('Insufficient paymaster token balance')
    } else {
      console.error('Transaction failed:', error.message)
    }
    throw error
  }
}
```

### Multi-Token Fee Payment

```javascript
async function sendTransactionWithDifferentToken(account) {
  try {
    // Send transaction paying fees with a different paymaster token
    const result = await account.sendTransaction({
      to: '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6',
      value: 1000000000000000000 // 1 ETH
    }, {
      paymasterToken: {
        address: '0xA0b86a33E6441b8c4C8C8C8C8C8C8C8C8C8C8C8C' // Different token
      }
    })
    
    console.log('Transaction paid with custom token:', result.hash)
    console.log('Fee paid:', result.fee, 'custom token units')
    
    return result
    
  } catch (error) {
    console.error('Custom token transaction failed:', error.message)
    throw error
  }
}
```

### Token Transfer with Gasless Fees

```javascript
async function transferTokensGasless(account) {
  try {
    // Quote the transfer first
    const transferQuote = await account.quoteTransfer({
      token: '0xA0b86a33E6441b8c4C8C8C8C8C8C8C8C8C8C8C8C', // Some ERC20 token
      recipient: '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6',
      amount: 1000000000000000000 // 1 token (18 decimals)
    })
    console.log('Transfer fee estimate:', transferQuote.fee, 'paymaster token units')
    
    // Execute the transfer
    const result = await account.transfer({
      token: '0xA0b86a33E6441b8c4C8C8C8C8C8C8C8C8C8C8C8C',
      recipient: '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6',
      amount: 1000000000000000000
    }, {
      transferMaxFee: 50000 // Maximum fee limit in paymaster token units
    })
    
    console.log('Gasless transfer hash:', result.hash)
    console.log('Transfer fee:', result.fee, 'paymaster token units')
    
    return result
    
  } catch (error) {
    if (error.message.includes('Exceeded maximum fee')) {
      console.error('Transfer fee too high, cancelled')
    } else if (error.message.includes('not enough funds')) {
      console.error('Insufficient paymaster token balance')
    } else {
      console.error('Transfer failed:', error.message)
    }
    throw error
  }
}
```
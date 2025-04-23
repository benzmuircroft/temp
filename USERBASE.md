# @ypear/userbase

A CRDT-based user management system with authentication, encryption, and peer-to-peer networking capabilities.

## Key Features

- **Distributed Architecture**: Built on Hypercore/Hyperbee for decentralized storage
- **End-to-End Encryption**: AES-256-CBC encryption using node-forge
- **Peer-to-Peer Networking**: Uses Hyperswarm/DHT for discovery and connectivity
- **Bot Prevention**: Sophisticated detection and prevention system
- **Secret Recovery**: Secure account recovery mechanism
- **Automatic hypercore trimming**: Accounts stay small

## Core Components

1. **User Management**:
   - Registration with referral system
   - Login with PIN-based authentication
   - Account recovery via secret key
   - Bot detection and cleanup

2. **Data Storage**:
   - Encrypted key-value storage
   - Pub/Sub system for data updates
   - Index management for distributed data

3. **Networking**:
   - Direct peer-to-peer communication
   - Broadcast messaging (todo: should use the router)
   - Connection management

## Advanced Functionality

- **Key Rotation**: Hierarchical key derivation system (todo: explain reword)
- **Data Migration**: Profile upgrade system with index fixing
- **Connection Handling**: Knockout system for single-instance enforcement
- **Bot Bounty System**: Automated bot account cleanup

## Installation
```bash
npm install @ypear/userbase
```

## Basic Usage
```javascript
const userbase = require('@ypear/userbase');

// Initialize with router and options
const ub = await userbase(router, {
  folderName: './db/userbase', // Storage location
  aes: {
    key: 'your-encryption-key', // 64-byte hex string
    iv: 'your-iv' // 32-byte hex string
  },
  quit: appQuitFunction, // Function to close the app
  loadingLog: (log) => console.log(log), // Optional progress logger
  loadingFunction: () => {}, // Optional loading callback
  onData: (change, data) => {}, // Data change handler
  botPrevent: async (lookup, get, sponsor) => {} // Bot prevention
});
```

## API

### Core Functions
- `register(username, referrer)`: Register new user
- `recover(username, secret)`: Recover user account
- `botDelete(username)`: Remove bot accounts
- `list()`: Get all users
- `get(username)`: Get user details

### Encryption
Uses node-forge for AES-256-CBC encryption with configurable key/IV.

### Network Layer
- Uses Hypercore for data storage
- Hyperswarm for peer discovery
- DHT for peer connectivity

## Options
| Parameter | Type | Description |
|-----------|------|-------------|
| folderName | string | Storage directory |
| aes.key | string | 64-byte hex encryption key |
| aes.iv | string | 32-byte hex initialization vector |
| quit | function | App shutdown function |
| loadingLog | function | Progress logging function |
| loadingFunction | function | Loading state callback |
| onData | function | Data change handler |
| botPrevent | function | Custom bot prevention logic |

## Security Considerations
- Always generate unique AES keys/IVs per installation
- Implement proper secret recovery flows
- Use the built-in bot prevention or provide custom logic

## Example
See `auth.js` for complete implementation example with:
- User registration
- Secret recovery
- Bot detection
- Peer networking

## License
MIT

## 👀 Description

## 💾 Installation

## 🧰 Methods

## ✅ Usage

## ⚠️ Misusage

## 🤯 Gotchas

## 📜 Licence

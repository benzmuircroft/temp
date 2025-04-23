# 🌐 @ypear/router

todo: redo this

## Core Networking Architecture

### 1. Connection Management
```javascript
initSwarm({
  seed: Buffer,       // Network identifier
  publicKey: String,  // Optional if seed provided
  userbase: {         // Userbase integration
    store: Hypercore,
    manager: AutobaseManager,
    onBroadcast: Function
  },
  cache: {            // Database integration
    synced: Boolean,
    peerClose: Function,
    selfClose: Function
  }
})
```

### 2. Message Propagation System
- **Tag-based Deduplication**: 5-minute expiration
- **Delivery Guarantees**: Confirmed propagation
- **Error Handling**: Automatic reconnection

### 3. Peer Management
- Hyperswarm discovery
- Protomux-RPC channels
- Graceful shutdown hooks

## API Reference

### `router.start(topic)`
```javascript
{
  topic: String,       // 32-byte swarm topic
  options: {
    server: Boolean,   // Accept incoming connections
    client: Boolean    // Make outgoing connections
  }
}
```

### `router.propagate(topic, data)`
1. Generates encrypted timestamp tag
2. Tracks peer responses
3. Cleans up expired tags

### `router.onConnection(peer)`
Handles:
- Stream replication
- RPC setup
- Error events

## Integration Example
```javascript
// Initialize with userbase
const router = await ypearRouter({}, {
  seed: '0xabc...',
  userbase: {
    store: userbaseStore,
    manager: autobaseManager,
    onBroadcast: handleMessage
  }
});

// Join network
await router.start('my-app');

// Send data
await router.propagate('update', {
  payload: data,
  timestamp: Date.now()
});
```

## License
MIT

## 😎 Gotchas

## ⚠️ Misusage

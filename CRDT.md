# @ypear/crdt.js - Distributed CRDT over Hyperswarm

A Conflict-Free Replicated Data Type (CRDT) implementation using Yjs with Hyperswarm networking and optional LevelDB persistence.

## Features

- **Distributed synchronization** via Hyperswarm network
- **CRDT operations** powered by Yjs
- **Optional persistence** using LevelDB
- **Map & Array support** with automatic conflict resolution
- **Batch operations** for atomic transactions
- **Efficient delta updates** with state vector tracking

## Installation
```bash
npm install @ypear/crdt
```

## Basic Usage
```javascript
(async () => {
  const router = await require('@ypear/router')(peers, {
    seed: '76e78c7efa235f018799125822feb0c4', // example only 32 hex (generate a different one)
    username: 'alice' // example only username
  });
  const crdt = await require('@ypear/crdt')(router, {
    topic: 'my-crdt-topic',
    leveldb: './my-database', // optional persistence
    observerFunction: (update) => {
      // Handle real-time updates
    }
  });

  // Map operations
  await crdt.map('users');
  await crdt.set('users', 'user1', {name: 'Alice'});
  await crdt.del('users', 'user1');

  // Array operations
  await crdt.array('messages');
  await crdt.push('messages', 'Hello');
  await crdt.unshift('messages', 'Hello');
  await crdt.insert('messages', 0, ['Welcome']);
  await crdt.cut('messages', 0, 1);

  // Batch operations

  // Batch Map operations
  await crdt.map('users', true);
  await crdt.set('users', 'user1', {name: 'Alice'}, true);
  await crdt.del('users', 'user1', true);

  // Batch Array operations
  await crdt.array('messages', true);
  await crdt.push('messages', 'Hello', true);
  await crdt.unshift('messages', 'Hello', true);
  await crdt.insert('messages', 0, ['Welcome'], true);
  await crdt.cut('messages', 0, 1, true);

  await crdt.execBatch();

  // todo: Array inside a Map


})();
```

## API Reference

### Core Methods

- `map(name)`: Creates/gets a CRDT map
- `set(mapName, key, value)`: Sets a value in a map
- `get(mapName, key)`: Gets a value from a map
- `del(mapName, key)`: Deletes a key from a map
- `array(name)`: Creates/gets a CRDT array
- `push(arrayName, value)`: Appends to an array
- `insert(arrayName, index, value)`: Inserts into an array
- `cut(arrayName, index, length)`: Removes from an array
- `execBatch(operations)`: Executes batched operations atomically

### Advanced Features

- **Observer Function**: Receive real-time updates via `options.observerFunction`
- **Direct Access**: Access current state via `crdt.c` property
- **Network Control**: Manual propagation via `crdt.propagate()`

## Persistence

When LevelDB persistence is enabled:
- All operations are stored in LevelDB
- State vectors track update history
- Documents reconstruct from persisted data on restart

## Network Synchronization

The CRDT automatically:
- Propagates updates to all peers
- Merges incoming updates
- Resolves conflicts using Yjs algorithms
- Maintains eventual consistency

## License
MIT

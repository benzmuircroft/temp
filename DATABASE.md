# @ypear/database

A robust CRDT-based database with LevelDB persistence, built on `@ypear/crdt`, featuring peer-to-peer synchronization and transactional operations.

## Key Features
- **Conflict-free Replicated Data Types (CRDT)** for automatic conflict resolution
- **LevelDB persistence** by default
- **Peer-to-peer synchronization** via `@ypear/router`
- **Transactional operations** are automatic with full audit trails
- **Fine-grained access control** with table and item-level rules
- **Time-based locking** for concurrent operations
- **Temporary networks** for bug protection
- **Atomic operations** with rollback capabilities
- **Built-in protection rules** for data integrity

## Installation
```bash
npm install @ypear/database
```

## Initialization
```javascript
const { db, GUB, EMPTY } = await require('@ypear/database')(router, {
  topic: 'your-topic',
  rules: {
    'tableName': { // Table with rules
      owner: 'publicKey', // Only this user may delete items
      'protectedItem': { 
        negAlow: true,  // Disallow negative values
        owner: 'publicKey' // Item owner
      }
    },
    'anotherTable': {} // Table with no rules
  }
});
```

## Core Concepts

### Transaction Handling 
All operations require a BUG transaction object:
```javascript
let BUG = GUB.NEW('temp', {}, new Error().stack, { 
  caller: 'transaction-description',
  hist: true  // todo: Enable history tracking
});

BUG.EXEC = async function(BUG) {
  // Database operations here
  GUB.END(BUG, null, () => console.log('Success'));
};
BUG.EXEC(BUG);
```

### Networks
- **live**: The authoritative synchronized state
- **temp**: Temporary network for testing operations
- **test**: Isolated testing environment

## API Reference

### Database Operations
- `db.mod(BUG, table, key, path, value, note)` - Modify or create data
- `db.can(BUG, table, key, note, callback)` - Access-controlled context
- `db.cut(BUG, table, key, path, note)` - Remove data
- `db.sum(BUG, table, key, path, op, value, note)` - Numeric operations
- `db.del(BUG, table, key, note)` - Delete an item
- `db.isLocked(BUG, table, key)` - Check if item is locked
- `db.iveLocked(BUG, table, key)` - Check if you have locked an item

### GUB (Global Utility Base)
- `GUB.NEW(env, args, trace, options)` - Create new transaction
- `GUB.END(BUG, error, callback)` - Complete transaction
- `GUB.LOG_LEVEL` - Control logging verbosity (0-2)
- `GUB.PAINTER` - Track last successful transaction


### Table Initialization And Protection Rules
Define access control when initializing your tables:
```javascript
rules: {
  tableName: {
    owner: 'publicKey', // Table-level permissions
    itemName: {         // Item-level permissions
      negAlow: true,    // Disallow negative values
      owner: 'publicKey'// Item owner
    }
  }
}
```

### Peer Configuration
Requires `@ypear/router` instance:
```javascript
const router = await require('@ypear/router')(peers, {
  seed: 'your-seed',
  username: 'your-username'
});
```

## Error Handling
- Failed transactions are automatically logged to BUGS directory
- Includes original error, call stack, and operation audit trail
- Automatic rollback of failed operations

## Development Notes
- Set `GUB.LOG_LEVEL = 2` for debug output
- Temporary networks (`temp`) are automatically cleaned up
- Use `BUG.TEST_ONLY` flag for development testing
- All operations are audited in `BUG.AUDIT`

## Example Usage
```javascript
const BUG = GUB.NEW('temp', {}, new Error().stack, { caller: 'example' });

BUG.EXEC = async function(BUG) {
  db.mod(BUG, 'animals', 'donkey', [], { is: 'new' }, 'creating');
  
  await db.can(BUG, 'animals', 'donkey', 'modifying', async function(BUG) {
    db.cut(BUG, 'animals', 'donkey', ['a', 'b'], 'removing field');
    db.sum(BUG, 'animals', 'donkey', ['a', 'bal'], 'add', '174', 'adding value');
  });
  
  GUB.END(BUG, null, () => console.log('Done'));
};

BUG.EXEC(BUG);
```

## License
MIT

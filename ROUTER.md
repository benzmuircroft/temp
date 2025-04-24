# 🌐 @ypear/router

## 👀 Description
A router runs a single Hyperswarm. It routes topics. It is modular and can support a @ypear/userbase, multiple @ypear/database's and multiple @ypear/crdt's but each database/crdt must be on it's own unique topic. You can use it without userbase but, you will need to start it with seed and username on your own.

## 💾 Installation
```bash
npm install @ypear/router
```

## 🧰 Methods


## ✅ Usage
See usage with @ypear/userbase instead.
```javascript
// Initialize without userbase
const router = await ypearRouter({}, {
  seed: '32 hex string', // unpacks to a determinilistic keyPair (you can get this after userbase.login)
  username: 'bob' // make up a name or got on userbase.login
});

await router.start('topic');

const [
  propagate, // wait till everyone propagates to everyone and replys done 
  broadcast, // same as propagate but no waiting for replys (noone replys)
  toPeers, // max 56 peers (noone replys)
  toPeer // to one peer (does not reply)
] = await router.alow(options.topic, async function handler(d) {
  // messages from other peers on this topic come through here
});

// Send data
await propagate(dataObject);

await broadcast(dataObject);

await toPeers(dataObject);

await toPeer(publicKey, dataObject);
```
## ⚠️ Misusage
todo.

## 🤯 Gotchas
- The userbase takes a router that has not been started.

- If the router has a userbase; the router will replicate the userbase's corestore.

- Propagate will send to each of your 64 which makes them propagate to there 64 and so-on and so-on but each person that receives it is tagged in the message and will not respond a second time by a hit from another peer. Each will wait until there entire group has responded/dropped-out/timesout to reply and this will cascade back to you like a sonar mapping a room.

- Each crdt/database added to the router, alter the routers opions.cache, therefore adding themselves to the router as topics

- a database automatically runs a crdt side-by-side under the hood; so each database will alter the routers options.cache twice ('databaseTopic' = database and 'databaseTopic-db' = crdt)

- `alow` achually lets you alow sub topics an can be used along side `deny`. In-other-words; you arn't forced to alow the topic that you `start`ed the router with!

## 📜 Licence
MIT

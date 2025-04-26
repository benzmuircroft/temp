# <img src="https://github.com/benzmuircroft/temp/blob/main/Yjs1.png" height="32" style="vertical-align:40px;" />🍐 @ypear/userbase 😀
# 🇾🍐😀 @ypear/userbase


## 💾 Installation
```bash
npm install @ypear/userbase
```

## 👀 Description
An autobase converted into a invite only registration, login and recovery system. Each user has a profile and can publish informaton to the undelying Corestore. Users can also list each other and look to see what other users have published. Single user instance is enforced.


## 🧰 Methods
Before registration or recovery:
```javascript
isYpearUserbase: true,
lookup,
getImages,
close: base.close,
recover,
nextKeyPair
register
```
Before login:
```javascript
isYpearUserbase: true,
lookup,
getImages,
close: base.close,
recover,
nextKeyPair
login,
list,
botDelete
```
After login:
```javascript
isYpearUserbase: true,
username,
success: 'success',
self: profile,
peer,
showPub,
call,
keyPair,
secret,
index,
get,
put,
knockout,
// hyperdown,
options,
aes,
store, // for cacheDB
pub, // statuses
sub, // statuses watcher
swapPublisher, // to change ownership of a published item
upgrade,
rename,
list,
indexOf,
sign
```

## ✅ Usage
```javascript
const ubExample = async function() {
  return new Promise(async (resolve) => {

    const loading = {
      steps: 10,
      stage: 0,
      outcome: true,
      more: function(done) {
         loading.stage ++;
         // send to client-side: { n: loading.stage * loading.steps, outcome: loading.outcome, done };
      }
   };
  
   loading.more();
  
  
  
  
   let router = require('@ypear/router'); // userbase will upgrade this later several times so it must be 'let'
  
   let seeSecret = false; // should be true but we have no ui here
  
  
   const userbase = await require('@ypear/userbase')(router, {
      networkName: 'myApp-example-123', // pick a unique name
      aes: { // choose your own aes key and iv for your app
         key: '581cecab27fc7724a871f9a5dc26030db03b6d9d850058c7b106497544415989', // keep same for all users!
         iv: '143cc663df5fb41611e0e4365b3c0e45', // keep same for all users!
      },
      entropy: 16,
      seeSecret: !seeSecret ? undefined : {
         username: seeSecret,
         send: function(data) { // user must never have logged in after join but never recieved their secret due to network/connection issue
         // your method to send the data to the client-side goes here ...
      }},
      // hyperdown: true, // forces the creation of hyperdown secrets like it does with userbase secrets
      quit: () => { throw 'quit'; }, // should be app.quit wrapper function passed from main.js
      loadingLog: function(log) {
         console.log('loading bar:', log); // send loading information to the client-side
      },
      loadingFunction: function() {
         loading.more();
      },
      onData: async function(change, data) { 
         if (loading.outcome == 'done'/* && is.options.filters.includes(change)*/) {
            // users could detect maintenance on/off for users that may have roles
            console.log('tester got some data:', data, 'on change:', change);
         }
      },
      botPrevent: async function(lookup, get, sponsor) { // prevent name hogging
         return false; // see: #botPreventExample
      }
   });
  
  loading.outcome = userbase.register ? 'join': undefined; // if they need to register still
  console.log('loading outcome?', loading.outcome);
  
  loading.more();
  
  
  
  
  
  
    async function exampleRecover(d) { // would be sent from the client-side
      const success = await userbase.recover(d.username, d.secret); // 'fail no profile' | 'fail verifier' | 'success'
      // you need to send the succes result back to the client-side
    }
  
  
  
  
    let exampleRegister;
    if (loading.outcome == 'join') {
      exampleRegister = async function(d) {
        d = {
          referrer: d.referrer,
          username: d.username
        };
        loading.steps = 10;
        loading.stage = 0;
        loading.more = function(done) {
          loading.stage ++;
          // send this to the client-side { n: (loading.stage * 100) / loading.steps, done };
        };
        const profile = {
          _id: d.username,
          spon: d.referrer,
          fp: global.fingerprint // you can use a device fingerprint lib to detect bots
        }
        const [success, secret, pin] = await userbase.register(profile.spon, profile._id, profile);
        // send this to the client-side { fail: (success != 'success'), secret, pin, report: success };
        return { fail: (success != 'success'), secret, pin, report: success };
      };
    }
  
  
  
  
  
  
    async function exampleLogin(d) {
      let login;
      d = {
         username: d.username,
         password: d.password
      };
      loading.steps = 4;
      loading.stage = 0;
      loading.outcome = true;
      loading.more = function(done) {
         loading.stage ++;
         // send to client-side { n: (loading.stage * 100) / loading.steps, outcome: loading.outcome, done };
      };
      const onCall = async function(soc, d) { // todo: probably (soc, keys, d) instead
         const reply = function (d) {
         soc.write(b4a.from(JSON.stringify(d)));
         };
         const remoteUserbasePublicKey = soc.remotePublicKey.toString('hex');
         if (is?.allowedMethods.includes(d[0])) { // todo: add some methods note reply just resolves for the one asking
            // is.serviceCalledYou should be setup by you later (use imagination)
            if (is?.serviceCalledYou) await is.serviceCalledYou(reply, remoteUserbasePublicKey, d[0], d[1]); // private 1-1 connect address for service-users to all talk to this user
            else reply('not ready'); // could be better
         }
         else {
            throw new Error(`thisScriptName.js onCall has no method ${d[0]}`);
         }
      };
      [login, router] = await userbase.login(d.password, d.username, onCall);
      if (login.success != 'success') {
         // reply to client-side { fail: true, report: login.success }
         console.trace({ fail: true, report: login.success });
      }
      else {
         const store = login.store;
         global.hide = login.aes.en;    // local encryption
         global.show = login.aes.de;    // local decryption
         // methods for the user's utilization
         is = {
            call:         login.call,      // is.call('method', remoteUserbasePublicKey, {});
            knockout:     login.knockout,  // await is.knockout(publicKey)
            aPublicKey:   login.showPub,   // enforce the correct publicKey decrypted length as to not make mistakes
            // hyperdown:    login.hyperdown, // keyPair for events
            options:      login.options,
            peer:         login.peer,      // is.peer(peerid), it finds users userbase imutable profile objects
            got:          login.get,       // await is.got(key)
            put:          login.put,       // await is.put(key, val)
            pub:          login.pub,
            sub:          login.sub,
            // botBounty
         };
         is.locked = async function(str, key, iv) {
            return global.hide(str, key || is.aPublicKey(my.self.userbase), iv || my.secret);
         };
         is.unlocked = async function(str, key, iv) {
            return global.show(str, key || is.aPublicKey(my.self.userbase), iv || my.secret);
         };
         // the user =======================================================================================================================================
         my = {
            _id:        login.username,  // string connected to your userbase input
            self:       login.self,      // like my.peer ...
            index:      login.index,     // records of your extra objects
            secret:     login.secret,    // string 32 length
            list:       userbase.list
         };
         my.cache = await is.got(my._id); 
         is.decached = async function() { // save your cache but, personally encrypt it so only you can recover it
            await is.put(
               my._id,                                                                 // key: username
               my.cache,                                                               // value: object
               my.cache.bot ? undefined : is.aPublicKey(my.self.userbase),             // secretKey will be privatly encrypted by this user
               my.cache.bot ? undefined : my.secret                                    // secretKey will be privatly encrypted by this user
            );
         };
         // the user's current state
         const network = await is.got('seed'); // ment for users to see after the seed user is created
         if (!my.cache) {
         my.cache = { // set profile for the first time
            genesis: +new Date(),
            activeOn: +new Date(),
            bot: true, // todo: change this when the user has human activity so that their account becomes protected.
            refs: [], // team
            anything: {},
            vol: 0.5, // things like setting volume
            sex: 'm',
            bg: '', // background ?
            base: 'btc'
         };
         await is.decached();
         }
         else {
         my.cache.activeOn = +new Date(); // we don't save here, instead we wait to see if the user does something un bot like ... // todo: maybe move to close but they must do something
         }
         // ... the user is logged in now (load your app stuff) ...
         console.log({
          //, store, network
          my, is
         }, '... the user is logged in now (load your app stuff) ...');
      }
    }
  
    resolve([
      userbase,
      exampleRegister,
      exampleRecover,
      exampleLogin
    ]);
  });
};
```
Using the example:
```javascript
(async () => {
  const [ userbase, register, recover, login ] = await ubExample();

  if (typeof register == 'function') {
    console.log('new user registering');
    console.log(await register({ referrer: 'seed', username: 'seed' }));
    /* prints something similar to:
    HypercoreError: STORAGE_EMPTY: No Hypercore is stored here
    at Core.resume (/home/benz/Desktop/vite/hypear/ypear/userbase/node_modules/hypercore/lib/core.js:91:15)
    at async Core.open (/home/benz/Desktop/vite/hypear/ypear/userbase/node_modules/hypercore/lib/core.js:56:14)
    at async Hypercore._openCapabilities (/home/benz/Desktop/vite/hypear/ypear/userbase/node_modules/hypercore/index.js:385:17)
    at async Hypercore._openSession (/home/benz/Desktop/vite/hypear/ypear/userbase/node_modules/hypercore/index.js:327:7) {
      code: 'STORAGE_EMPTY'
    }
    loading bar: removing userbase ...
    loading bar: creating ramstore ...
    loading bar: store ready ...
    loading bar: store get input ...
    loading bar: store get output ...
    loading bar: input ready ...
    loading bar: output ready ...
    loading bar: creating autobase ...
    loading bar: autobase setup ...
    loading bar: autobase ready ...
    loading bar: create manager ...
    loading bar: manager ready ...
    loading bar: create hyperswarm ...
    loading bar: joining swarm ...
    true is synced at start?
    loading bar: swarm flushing ...
    loading bar: performing task wait ...
    loading bar: 
    userbase done?
    loading outcome? join
    register: [AsyncFunction: exampleRegister]
    loading bar: registering ...
    loading bar: has options.keyPair false ...
    loading bar: has secret false ...
    loading bar: creating key pair ...
    loading bar: hiding secret ...
    loading bar: secret hidden ...
    loading bar: restarting base after creating secret ...
    loading bar: creating corestore ...
    loading bar: store ready ...
    loading bar: secure channel setup ...
    loading bar: getting index ...
    loading bar: index ready ...
    loading bar: index updating ...
    loading bar: store get input ...
    loading bar: store get output ...
    loading bar: input ready ...
    loading bar: output ready ...
    loading bar: creating autobase ...
    loading bar: autobase setup ...
    loading bar: autobase ready ...
    loading bar: create manager ...
    loading bar: manager ready ...
    loading bar: create hyperswarm ...
    loading bar: joining swarm ...
    true is synced at start?
    loading bar: swarm flushing ...
    loading bar: performing task register ...
    loading bar: doing register ...
    loading bar: registering ...
    loading bar: has options.keyPair true ...
    loading bar: creating userbase profile ...
    loading bar: adding userbase profile ...
    loading bar: 
    {
      fail: false,
      secret: '5c4613695592466547117c1d48128745', <<<< yours will be different (save it)
      pin: '5c4745',                              <<<< yours will be different (save it)
      report: 'success'
    }
    */
  }
  else { // on second run of this script we login
    const username = 'seed';
    const password = '5c4745'; // your pin generated in step one
    console.log(await login({ username, password }));
    /*
      secret: 5c4613695592466547117c1d48128745
      loading bar: creating corestore ...
      loading bar: store ready ...
      loading bar: secure channel setup ...
      loading bar: getting index ...
      loading bar: index ready ...
      loading bar: index updating ...
      loading bar: store get input ...
      loading bar: store get output ...
      loading bar: input ready ...
      loading bar: output ready ...
      loading bar: creating autobase ...
      loading bar: autobase setup ...
      loading bar: autobase ready ...
      loading bar: create manager ...
      loading bar: manager ready ...
      loading bar: create hyperswarm ...
      loading bar: joining swarm ...
      true is synced at start?
      loading bar: swarm flushing ...
      loading bar: performing task wait ...
      loading bar: 
      userbase done?
      loading outcome? undefined
      loading bar: compairing pin to secret ...
      loading bar: getting profile ...
      loading bar: knocking out other account instances ...
      Client errored: DHTError: PEER_NOT_FOUND: Peer not found
         at findAndConnect (/home/benz/Desktop/vite/hypear/ypear/userbase/node_modules/hyperdht/lib/connect.js:350:74)
         at async connectAndHolepunch (/home/benz/Desktop/vite/hypear/ypear/userbase/node_modules/hyperdht/lib/connect.js:181:3) {
      code: 'PEER_NOT_FOUND'
      }
      false
      server listen: fd0bd07fc4fca35ff93aa607cd7f2d11e7e824bf0f0033cc3bbe9600037a28de
      loading bar: creating dht server ...
      loading bar: dht server ready ...
      loading bar: dht server listening ...
      loading bar: 
      {
         my: {
            _id: 'seed',
            self: {
               _id: 'seed',
               spon: 'seed',
               sig: 'd0a8378760cf4c2814e7e95d9c5546c39679c1593b98680d314dd9d6969837e4db2a81e47f42a25ff3e2fe6a35a453d87d40eb7fec8ff85d046d805f9a22150b',
               userbase: '629683e2b7c3bdd8aaf6783a237cc22566775fa2719eef4fea8fc791560af74b508e08238b2c4b08bd8232e7ae6a52bf2f57638ff3d8b3115be6e5848243be0f7b2afa91c994f4eb',
               phone: '96ca171e9aebb6bcb519a1b61910079cd99671296c175ae5077cce97bbc75f58dba621f16bd96c1a4672360cfed4de75783036e2dcd82c3ee1de7fc64df4eabc0b19fa1c64f29f79',
               trunc1: '4918d7a1704d4c6c3d298cc269ba3bb41bc379cdaf450ff5c10832d3cf36f081c65553fe27beb6d7f2aa5a812cf8b31b63c4bcc1188616f8ef9a5a97e1896ccbb9919d0e6bd0c2aa',
               trunc2: 'cdac68de5d29f9905784aa7870d15ee55d52352cef082bce192ae574cd103cde1e0fa18f0cdd7f03d3059709d381036647b8f4c06e8cc9d4a7047d11661c13d1fbd4b62c3b09fa26'
            },
            index: { seed: [Object] },
            secret: '5c4613695592466547117c1d48128745',
            list: [AsyncFunction: list],
            cache: {
               genesis: 1745668058479,
               activeOn: 1745668237995,
               bot: true,
               refs: [],
               anything: {},
               vol: 0.5,
               sex: 'm',
               bg: '',
               base: 'btc'
            }
         },
         is: {
            call: [AsyncFunction: call],
            knockout: [AsyncFunction: knockout],
            aPublicKey: [Function: showPub],
            options: {
               folderName: './db/userbase',
               aes: [Object],
               entropy: 16,
               seeSecret: undefined,
               quit: [Function: quit],
               loadingLog: [Function: loadingLog],
               loadingFunction: [Function: loadingFunction],
               onData: [AsyncFunction: onData],
               botPrevent: [AsyncFunction: botPrevent],
               keyPair: [Object],
               role: 'seed'
            },
            peer: [AsyncFunction: peer],
            got: [AsyncFunction: get],
            put: [AsyncFunction: put],
            pub: [AsyncFunction: pub],
            sub: [AsyncFunction: sub],
            locked: [AsyncFunction (anonymous)],
            unlocked: [AsyncFunction (anonymous)],
            decached: [AsyncFunction (anonymous)]
         }
      }
      ... the user is logged in now (load your app stuff) ...
    */
  }




})();
```
## ⚠️ Misusage
todo.

## 🤯 Gotchas
- The `userbase` takes a `router` that has not been `started`

- The `router` will automatically replicate the `userbase`'s `corestore`

- The first user added to a new network must register with `referrer: 'seed', username: 'seed'`, the second must have `referrer: 'seed'`, afterwhich users may start using existing users as thier referrer

- When testing, please remember to have at least the seed or another user online when creating a new user, otherwise the new user will not see the network

- `is.put`/`is.got` are for updating your profile and `is.pub`/`is.sub` is for data that is not in your profile but will be indexed by your profile so that other users can find it 

- `userbase` will update all subscribed users on any `userbase.put` if `dataEvent` is true. To subscribe to a prfile put; run `is.options.filters = ['username1', 'username2' ...];` after login 

## 📜 Licence
MIT


# todo:
let put use pub or take away onData

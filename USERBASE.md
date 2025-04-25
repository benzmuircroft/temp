# 🕳️🥊😀 @ypear/userbase


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
See usage with @ypear/userbase instead.
```javascript
(async () => {

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




   const userbase = await require('@ypear/userbase')(router, {
      folderName: './db/userbase',
      aes: { // choose your own aes key and iv for your app
         key: '64 length hex', // keep same for all users!
         iv: '32 length hex', // keep same for all users!
      },
      entropy: 16,
      seeSecret: !seeSecret ? undefined : {
         username: seeSecret,
         send: function(data) { // user must never have logged in after join but never recieved their secret due to network/connection issue
         // your method to send the data to the client-side goes here ...
      }},
      // hyperdown: true, // forces the creation of hyperdown secrets like it does with userbase secrets
      quit: global.quit, // should be app.quit wrapper function passed from main.js
      loadingLog: function(log) {
         // send loading information to the client-side
      },
      loadingFunction: function() {
         loading.more();
      },
      onData: async function(change, data) { 
         if (loading.outcome == 'done') {
            // users could detect maintenance on/off for users that may have roles
            console.log('tester got some data:', data, 'on change:', change);
         }
      },
      botPrevent: async function(lookup, get, sponsor) { // prevent name hogging
         return false; // see: #botPreventExample
      }
   });

  loading.outcome = userbase.register ? 'join': undefined; // if they need to register still
  
  loading.more();






   async function exampleRecover(d) { // would be sent from the client-side
      const success = await userbase.recover(d.username, d.secret); // 'fail no profile' | 'fail verifier' | 'success'
      // you need to send the succes result back to the client-side
   }





   if (loading.outcome == 'join') {
      async function exampleRegister(d) {
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
      };
   }






   async function exampleLogin(d) {
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
      const login = await userbase.login(d.password, d.username, onCall);
      if (login.success != 'success') {
         // reply to client-side { fail: true, report: login.success }
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
            botBounty
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
         const network = await is.got('seed');
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
      }
   }

})();
```
## ⚠️ Misusage
todo.

## 🤯 Gotchas
- The `userbase` takes a `router` that has not been `started`

- The `router` will automatically replicate the `userbase`'s `corestore`

- `is.put`/`is.got` are for updating your profile and `is.pub`/`is.sub` is for data that is not in your profile but will be indexed by your profile so that other users can find it 

- `userbase` will update all subscribed users on any `userbase.put` if `dataEvent` is true. To subscribe to a prfile put; run `is.options.filters = ['username1', 'username2' ...];` after login 

## 📜 Licence
MIT

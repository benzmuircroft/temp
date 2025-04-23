
# @ypear/nextts

Precision Time Boundary Utilities

## Core Functions

```js
// From nextts.js implementation:
const nextts = require('@ypear/nextts');

// Returns timestamp for next day at 00:00:00
const tomorrow = nextts.nextday(); 

// Returns timestamp for next hour at :00
const nextHour = nextts.nextHour();

// Returns timestamp for next minute at :00
const nextMinute = nextts.nextMinute();
```

Installation

```sh
npm install @ypear/nextts
```

API Reference

nextday([date])

- Parameters:

  - date (Optional): Date object or timestamp

Returns: Unix timestamp of next day at 00:00:00

Example:

```js
// From line 22-28
const nextDay = nextts.nextday(); 
// Returns timestamp for tomorrow 00:00:00
```

nextHour([date])
 - Parameters:
   - date (Optional): Date object or timestamp

Returns: Unix timestamp of next hour at :00

Example:

```js
// From line 29-44
const nextHr = nextts.nextHour();
// Returns timestamp for next hour boundary
```

nextMinute([date])

Parameters:
 - date (Optional): Date object or timestamp

Returns: Unix timestamp of next minute at :00

Example:

```js
// From line 45-50
const nextMin = nextts.nextMinute();
// Returns timestamp for next minute boundary
```

every(timing, callback)

- Parameters:

  - timing: 'nextday' | 'nextHour' | 'nextMinute'

  - callback: Function receiving timestamp

Example:

```js
// From line 79-114
nextts.every('nextMinute', (ts) => {
  console.log('Minute boundary:', ts);
});
```

checkEvery(timing, intervalMs, callback)

- Parameters:
  
  - timing: 'nextday' | 'nextHour' | 'nextMinute'
  - intervalMs: Check interval in milliseconds
  - callback: Function receiving timestamp

Example:

```js
// From line 115-122
nextts.checkEvery('nextHour', 15000, (ts) => {
  console.log('Hour check:', ts);
});
```

Basic Usage Example

```js
const nextts = require('@ypear/nextts');

// Schedule a daily task
setInterval(() => {
  nextts.every('nextday', (dayStart) => {
    console.log('New day started at:', new Date(dayStart));
    // Daily maintenance tasks here
  });
}, 60000);
```

License
MIT

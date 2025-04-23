# @ypear/forks

Local forks of Holepunch and related modules needed by userbase

## Included Modules

- `autobase`: Local fork of Holepunch's Autobase
- `@lejeunerenard/autobase-manager`: Fork of autobase-manager
- `hyperbee`: Fork with CAS prev-null support

## Installation

```bash
npm install @ypear/forks
```

## Usage

```javascript
const {
  autobase,
  autobaseManager,
  hyperbee
} = require('@ypear/forks');
```

## Why Forked?

These modules contain custom modifications needed for:
- Offline-first/local-first applications
- Specialized database operations
- Compatibility with userbase requirements

## Development

To modify any of the forked modules:
1. Edit the module in its subdirectory
2. Run `npm install` to update symlinks
3. Test changes in your application

## License

MIT © Benz Muircroft

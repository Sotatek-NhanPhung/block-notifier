# block-notifier

## Overview
Provide a standalone service, that can notify blockchain events via many channels
```
(listener) <-webhook- (notifier) <-filtered events- (watcher) <-crawled data- (blockchain network)
```
Packages:
- block-notifier
- base-watcher
- eth-watcher
- btc-watcher

## Usage
### Examples
```js
const BlockNotifier = require('block-notifier');
const notifier = BlockNotifier.create({
  outputs: [
    {
      type: BlockNotifier.OUTPUT_TYPE.WEBHOOK,
      url: 'http://example.com'
    },
    {
      type: BlockNotifier.OUTPUT_TYPE.REDIS_PUBSUB,
      host: '127.0.0.1',
      ...
    }
  ]
});

const getWatchingAddresses = function(){...} // Method to get watching addresses with current logic.
const isWatchingTransaction = function(tx){...} // Method to filter out watching transaction
const EthWatcher = require('eth-watcher');
const events = [
  {
    type: EthWatcher.EVENT.TRANSFER,
    filter: EthWatcher.createFilter({toAddress: '0x140427a7D27144A4cDa83bD6b9052a63b0c5B589'})
  },
  {
    type: EthWatcher.EVENT.ERC20_TRANSFER,
    filter: EthWatcher.createFilter({
      tokenAddress: '0xd26114cd6EE289AccF82350c8d8487fedB8A0C07', // OmiseGO
      toAddress: getWatchingAddresses
    })
  },
  {
    type: EthWatcher.EVENT.TRANSFER,
    filter: EthWatcher.createFilter({txFilter: isWatchingTransaction})
  },
];
const watcher = EthWatcher.create({
  notifier,
  events,
});
watcher.start();

```

### Classes
#### BaseWatcher
- Can be extended to ETHWatcher, BTCWatcher, etc...
- `start()` (void): ignite the engine
- `$create(WatcherConfig)` (BaseWatcher): create watcher instance
- `$createFilter(FilterConfig)` (BaseFilter): create events filter for watcher instance
#### FilterConfig
- `type`: can be TRANSFER, ERC20_TRANSFER, ... (for Ethereum, there may be other values for other platform)
#### BlockNotifier
- TBD...

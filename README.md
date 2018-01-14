bitcoind-rpc-monoeci.js
===============

[![NPM Package](https://img.shields.io/npm/v/bitcoind-rpc-monoeci.svg?style=flat-square)](https://www.npmjs.org/package/bitcoind-rpc-monoeci)
[![Build Status](https://img.shields.io/travis/yoyae/bitcoind-rpc-monoeci.svg?branch=master&style=flat-square)](https://travis-ci.org/yoyae/bitcoind-rpc-monoeci)
[![Coverage Status](https://img.shields.io/coveralls/yoyae/bitcoind-rpc-monoeci.svg?style=flat-square)](https://coveralls.io/r/yoyae/bitcoind-rpc-monoeci?branch=master)

A client library to connect to monoeci Core RPC in JavaScript.

## Get Started

bitcoind-rpc-monoeci.js runs on [node](http://nodejs.org/), and can be installed via [npm](https://npmjs.org/):

```bash
npm install bitcoind-rpc-monoeci
```

## RpcClient

Arguments : 

	- protocol : (string - optional) - (default: 'https') - Set the protocol to be used. Either `http` or `https`.
	- user : (string - optional) - (default: 'user') - Set the user credential.
	- pass : (string - optional) - (default: 'pass') - Set the password credential.
	- host : (string - optional) - (default: '127.0.0.1') - The host you want to connect with.
	- port : (integer - optional) - (default: 24157) - Set the port on which perform the RPC command.
	
## Examples

```javascript
var run = function() {
  var bitcore = require('bitcore');
  var RpcClient = require('bitcoind-rpc-monoeci');

  var config = {
    protocol: 'http',
    user: 'user',
    pass: 'pass',
    host: '127.0.0.1',
    port: 24157
  };

  var rpc = new RpcClient(config);

  var txids = [];

  function showNewTransactions() {
    rpc.getRawMemPool(function (err, ret) {
      if (err) {
        console.error(err);
        return setTimeout(showNewTransactions, 10000);
      }

      function batchCall() {
        ret.result.forEach(function (txid) {
          if (txids.indexOf(txid) === -1) {
            rpc.getRawTransaction(txid);
          }
        });
      }

      rpc.batch(batchCall, function(err, rawtxs) {
        if (err) {
          console.error(err);
          return setTimeout(showNewTransactions, 10000);
        }

        rawtxs.map(function (rawtx) {
          var tx = new bitcore.Transaction(rawtx.result);
          console.log('\n\n\n' + tx.id + ':', tx.toObject());
        });

        txids = ret.result;
        setTimeout(showNewTransactions, 2500);
      });
    });
  }

  showNewTransactions();
};
```

## Help 

You can dynamically access to the help of each method by doing
```
const RpcClient = require('bitcoind-rpc-monoeci');
var client = new RPCclient({
    protocol:'http',
    user: 'monoeci',
    pass: 'local321', 
    host: '127.0.0.1', 
    port: 24157
});

var cb = function (err, data) {
    console.log(data)
};
client.help(cb); //Get full help
client.help('getinfo',cb); //Get help of specific method
```
## License

**Code released under [the MIT license](https://github.com/bitpay/bitcore/blob/master/LICENSE).**

Copyright 2013-2014 BitPay, Inc.

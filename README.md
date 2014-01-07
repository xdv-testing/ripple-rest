# `ripple-simple.js`

A set of tools to simply connect to the Ripple network. The components are:

1. [ripple-simple.js Library](README.md#1-ripple-simplejs-library)
2. [Simplified REST API](README.md#2-simplified-rest-api)
3. [Command Line Tools](README.md#3-command-line-tools)





## 1. `ripple-simple.js` Library

Built on top of [`ripple-lib`](https://github.com/ripple/ripple-lib/), this library simplifies Ripple transaction submission and account activity analysis.

```js
var Simple = require('ripple-simple');

var simply = new RippleRippleSimple({
	// Simple options
});


// Simple Transaction Submission

simply.sendPayment(SimplePayment, callback);

simply.createOrder(SimpleOrder, callback);

simply.setTrustline(SimpleTrust, callback);


// Simple Account Activity Analysis

simply.confirmTransaction(txHash, function(status){ ... });

simply.getTransaction(txHash, function(transaction){ ... });

simply.getNextTransaction(txHash, function(transaction){ ... });

simply.monitorAccount(rpAddress, function(message){ ... });

simply.stopMonitoringAccount(rpAddress);


// Simple Server Monitoring

simply.getTransactionQueue(function(pendingTransactions){ ... });

simply.getServerStatus(function(status){ ... });

```


### Core features

1. [Simplified transaction formats](README.md#1-simplified-transaction-formats)
2. [Robust transaction submission](README.md#2-robust-transaction-submission)
3. [Definitive transaction confirmation](README.md#3-definitive-transaction-confirmation)
4. [Account activity monitoring](README.md#4-account-activity-monitoring)
5. [Optional connection to persistent data store](README.md#5-optional-connection-to-persistent-data-store)



#### 1. Simplified transaction formats

##### Simplified Payment Transaction

```js
{
	srcAddress: 'rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh',
	dstAddress: 'rpvfJ4mR6QQAeogpXEKnuyGBx8mYCSnYZi',
	dstAmount: {
		value: 100.0,
		currency: 'XRP',
		issuer: '' // empty string for XRP, gateway address for all other currencies
	}
}
```

###### Standard parameters

* `srcAddress` (optional if Simple is instantiated with `accounts`)
* `dstAddress`
* `srcAmount`
* `dstAmount`

###### Advanced parameters

* `slippage`
* `srcTag`
* `srcID`
* `dstTag`
* `dstID`
* `srcBalances`
* `txPaths`
* `expireAfter` // time or ledger_index?

[what other options do we need?]


##### Simplified Order Transaction

TODO

##### Simplified Trust Transaction

TODO




#### 2. Robust transaction submission

`ripple-simple.js` will submit transactions to the Ripple network and monitor newly closed ledgers to definitively confirm that the transaction has been validated and written into the ledger. If the transaction submission fails it will throw an error or, if the error is not due to the transaction being invalid, it will try again until the transaction is definitively confirmed.

```js
simply.sendPayment({
	// Simplified Payment Transaction
}, function(err, res){
	// See below for err and res formats
});
```

```js
simply.createOrder({
	// Simplified Order Transaction
}, function(err, res){
	
});
```

```js
simply.setTrustline({
	// Simplified Trustline Transaction
}, function(err, res){
	
});
```


##### `err` format

[which errors should this throw, which should it handle?]

##### `res` format

[should this return 'submitted' or wait until it is processed and return 'processed'?]



#### 3. Definitive transaction confirmation

`ripple-simple.js` will definitively confirm whether particular transactions have entered the Ripple ledger, are pending submission or validation, or have failed.

```js
simply.confirmTransaction(txHash, function(status){
	
});
```

```js
simply.getTransaction(txHash, function(transaction){
	
});
```


#### 4. Account activity monitoring

`ripple-simple.js` can be used to monitor the Ripple network for incoming, outgoing, or other transactions that affect a particular account.

[should i include the message functions Arthur laid out in the Ripple Cookbook here? like `scan`, `mark`, and `info`]

```js
simply.monitorAccount(srcAddress, function(message){
	
});
```

```js
simply.getNextTransaction(txHash, function(transaction){
	
});
```


#### 5. Optional connection to persistent data store

The `Simple` instance can be created with a connection to a persistent database to maintain the pending transaction queue and account activity log even if the process dies.


```js
var simply = new RippleSimple({
	db: ...
});
```

[how should we handle this? i'm thinking of having some kind of standardized db client that expects `save`, `read`, `list`, and `search` functions or something like that]




## 2. Simplified REST API

This is an Express.js app that utilizes `ripple-simple.js` to provide robust transaction submission, definitive transaction confirmation, and account activity monitoring over HTTP.

The API accepts JSON commands POSTed to routes following the form `/simply/sendPayment`. Commands all have the same names as `ripple-simple.js` library functions and expect the JSON objects submitted to have the same options as specified in the library's docs.

[should there be a single route with different methods or is this way easier?] 





## 3. Command Line Tools

Similar to the Simplified REST API, this toolset and daemon provides a command line interface to the `ripple-simple.js` functions.

In the future, the Command Line Tools daemon will provide support for multiple users and accounts running on the same machine.

[should the commands and formats be different for the command line?]

[could the Command Line Tools just be another interface attached to the same daemon that will support the REST API?]


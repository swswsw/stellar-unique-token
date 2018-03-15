# Stellar Unique Token
Unique (non-fungible) token standard for stellar.

This proposal seeks to create a simple standard specification for Non-fungible tokens (NFT) on Stellar.  The proposal is simple to implement and requires no modification to the Stellar protocol.

The draft of the proposal can be seen at [sep-xxx2.md](./sep-xxx2.md).  


# Specification #

## Sequence of creating non-fungible asset ##


	1. issuer issues an asset.  (see https://www.stellar.org/developers/guides/concepts/assets.html)
	2. the metadata of the asset (description, etc) are place in an ipfs or put on a certain url.
	3. the metadata url is added to the issuer's account data key-value data (using manageData operation in REST api) 
	4. issuer's multisig weight is changed so that the issuer can no longer issue the token.

For more information, please see the draft.


# Sample code to implement the draft #

Sample code to 
1. trust the asset 
2. issue/send the asset with attached memo.

```
// create new asset
var asset1 = new StellarSdk.Asset('20180311', issuerKey.publicKey());

//First, the receiving account must trust the asset
server.loadAccount(distributorKey.publicKey())
  .then(function(receiver) {
    var transaction = new StellarSdk.TransactionBuilder(receiver)
      // The `changeTrust` operation creates (or alters) a trustline
      // The `limit` parameter below is optional
      .addOperation(StellarSdk.Operation.changeTrust({
        asset: asset1,
        limit: '0.0000001'
      }))
      .build();
    transaction.sign(distributorKey);
    return server.submitTransaction(transaction);
  })

  // Second, the issuing account actually sends a payment using the asset
  .then(function() {
    return server.loadAccount(issuerKey.publicKey())
  })
  .then(function(issuer) {
    var transaction = new StellarSdk.TransactionBuilder(issuer)
      .addOperation(StellarSdk.Operation.payment({
        destination: distributorKey.publicKey(),
        asset: asset1,
        amount: '0.0000001'
      }))
      // add a memo (https://www.stellar.org/developers/learn/concepts/transactions.html)
   	  .addMemo(StellarSdk.Memo.text('metadata url'))
      .build();
    transaction.sign(issuerKey);
    return server.submitTransaction(transaction);
  })
  .catch(function(error) {
    console.error('Error!', error);
  });

```

Sample code to change the weight so the issuer account cannot issue more token.

```
server.loadAccount(sourceKeys.publicKey())
  // If the account is not found, surface a nicer error message for logging.
  .catch(StellarSdk.NotFoundError, function (error) {
    throw new Error('The source account does not exist!');
  })
  // If there was no error, load up-to-date information on your account.
  .then(function() {
    return server.loadAccount(sourceKeys.publicKey());
  })
  .then(function(sourceAccount) {
    // Start building the transaction.
    var transaction = new StellarSdk.TransactionBuilder(sourceAccount)
      .addOperation(StellarSdk.Operation.setOptions({
    	masterWeight: 0, // Weight of the master key. 
      }))
      .build();
    // Sign the transaction to prove you are actually the person sending it.
    transaction.sign(sourceKeys);
    // And finally, send it off to Stellar!
    return server.submitTransaction(transaction);
  })
  .then(function(result) {
    console.log('setOptions: Success! Results:', result);
  })
  .catch(function(error) {
    console.error('setOptions: Something went wrong!', error);
  });
```

More examples can be found in [example folder](./example/)

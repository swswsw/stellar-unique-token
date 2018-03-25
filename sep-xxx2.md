# Preamble #

```
SEP: 
Title: Stellar Unique Token (Non-fungible Token)
Author: swswsw
Status: Draft
Created: 2018-02-25
```


# Simple Summary #

This proposal specifies a simple way to implement unique tokens.

Every unique token should satisfy the following properties:
- only single token is ever issued (max issuance is 1)
- some metadata is attached



# Motivation #

Unique token is useful to create crypto collectibles, a necessary component to applications such as CryptoKitties.  



# Rationale #

The proposal aims to:
- require no change to Stellar protocol  
- allow anyone to verify the data (maximum issuance and metadata) 



# Specification #

## Sequence of creating non-fungible asset ##


	1. issuer issues an asset.  (see https://www.stellar.org/developers/guides/concepts/assets.html)
	2. the metadata of the asset (description, etc) are stored on ipfs or stored on somewhere that can be accessed through an url.
	3. the metadata url is added to the issuer's account data key-value data (using manageData operation in REST api) 
	4. issuer's multisig weight is changed so that the issuer can no longer issue the token.


The url of the metadata is associated with the token in the issuer's account data (step 3).  The key of the key/value data is the code of the token.  

eg. if the token has code "star"
the key used for account data is "star".  The key/value data will be
{
	name: "star",
	value: "http://stellar.org/nft_sample/data.json"
}


## Additional requirement ##
- only 1 token is issued before step 4 so that no more token can be issued
- the metadata should contain a json.  the json should contains at least the following properties:
```
{
  code: "",
  name: "",
  description: "",
  fungible: false 
}

```



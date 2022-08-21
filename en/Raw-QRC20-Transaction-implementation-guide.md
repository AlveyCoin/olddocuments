## Raw ARC20 Transaction implementation guide

##### Introduction

The intended audience for this document is developers who need to build transactions that interact with ARC20 tokens. This document consists of two parts. The first part describes how to execute ARC20 methods and watching for events by using various RPC calls on a fully online alveyd node. The second part describes how to create ARC20 transactions without using alveyd but rather using an example Bitcoin library to achieve the same functionality. The document assumes the reader is familiar with raw Bitcoin transactions as well as the ERC20 standard and the ABI of Ethereum.



##### Alvey's ARC20 standard

Alvey's ARC20 standard is virtually identical to Ethereum's ERC20 standard. A ARC20 token contract in Alvey typically would use the same template solidity contract as an ERC20 token. The only major exception being that Alvey's ARC20 tokens typically use 8 decimal points instead of 18 in Ethereum. 



##### Calldata for ARC20 calls

Calldata in OP_CALL scripts must be encoded according to the rules of the Ethereum ABI, e.g. the calldata is typically prefixed by the 4-byte function identifier. Since the word size is 256 bits, each value should be padded to 32 bytes.

###### Example of transfer calldata

If we want to transfer 1.1 tokens from an address we control to the address "qL1ah15xwmxLL75TBxfwiXpoovn6dKV72h", we first set the 4 byte prefix calldata to the transfer(address,uint256) function ("a9059cbb"). Next, we translate the receiver address to its 20-byte representation in hex: "1ae4b1d517dc7d62cec8739aa3a5a8fa10c9260d" (this can be done by using the **gethexaddress** RPC. After this, we should encode the number of tokens we wish to transfer. This value should be multiplied by the number of decimals of the ARC20 contract. Assuming that the contract we want to call uses 8 decimals, we would therefore encode this value as 68e7780 (1.1*10^8). Next, we left pad the receiver and amount with zeroes to 32 bytes and concatenate the arguments. We would therefore have the calldata: "a9059cbb 0000000000000000000000001ae4b1d517dc7d62cec8739aa3a5a8fa10c9260d 00000000000000000000000000000000000000000000000000000000068e7780".



##### Alveyd RPC

Alveyd uses the same style of RPC interface as bitcoind. Most of the RPCs available to bitcoind are available in alveyd, as well as a few Alvey specific ones. Below follows a description of a few RPCs that are useful when creating or watching ARC20 token transactions.

* **waitforlogs** - Used for listening for new events, e.g., in the context of ARC20 tokens it can be used to listen for new Transfer() events.
* **createrawtransaction** - Same functionality as the bitcoind equivalent with the addition of supporting OP_CALL transactions.
* **gettransactionreceipt** - Used to get a transaction receipt that provides information of any events emitted and the execution state of a transaction (e.g., gas used and whether an exception was triggered).
* **callcontract** - Used to perform a read-only execution of a contract. Can be used to, for example, execute balanceOf() or decimals() methods of a ARC20 contract.
* **sendtocontract** - Used to create and sign a contract transaction that is broadcast to the network. Can for example be used to execute the transfer() or approve() methods of a ARC20 contract.
* **gethexaddress/fromhexaddress** - Used convert Alvey's bitcoin-style base58 addresses to and from their 20-byte representation in hex.



### Examples of ARC20 contract execution

Below follows several examples executing the various functions of a ARC20 contract. These examples are run against a alveyd node running in regtest mode. You can run these examples against a testnet or mainnet node by changing the -regtest argument to -testnet or -mainnet. These examples depend on jq, python3, alveyd, and alvey-cli being installed on the target system.

##### Setting up a regtest alveyd node and deploying a test ARC20 contract.

To run these examples, we set up a regtest alveyd node, generate some blocks and deploy a ARC20 contract below.

```
ARC20_SENDER_PRIVKEY="cVNrcCHX5q318dDkpMFoLD1ack7TybxEjFB44xCsv5sW35kM7QuE"
ARC20_SENDER="qauZFnmbNBNuY2ujQateDwzvL6zoxBiY3H"
ARC20_EXAMPLE_CONTRACT_BIN="6080604052600860ff16600a620000179190620000e0565b633b9aca006200002891906200021d565b6000553480156200003857600080fd5b50600054600160003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002081905550620002c4565b6000808291508390505b6001851115620000d757808604811115620000af57620000ae62000288565b5b6001851615620000bf5780820291505b8081029050620000cf85620002b7565b94506200008f565b94509492505050565b6000620000ed826200027e565b9150620000fa836200027e565b9250620001297fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff848462000131565b905092915050565b60008262000143576001905062000216565b8162000153576000905062000216565b81600181146200016c57600281146200017757620001ad565b600191505062000216565b60ff8411156200018c576200018b62000288565b5b8360020a915084821115620001a657620001a562000288565b5b5062000216565b5060208310610133831016604e8410600b8410161715620001e75782820a905083811115620001e157620001e062000288565b5b62000216565b620001f6848484600162000085565b9250905081840481111562000210576200020f62000288565b5b81810290505b9392505050565b60006200022a826200027e565b915062000237836200027e565b9250817fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff048311821515161562000273576200027262000288565b5b828202905092915050565b6000819050919050565b7f4e487b7100000000000000000000000000000000000000000000000000000000600052601160045260246000fd5b60008160011c9050919050565b610f5380620002d46000396000f3fe608060405234801561001057600080fd5b506004361061009e5760003560e01c80635a3b7e42116100665780635a3b7e421461015d57806370a082311461017b57806395d89b41146101ab578063a9059cbb146101c9578063dd62ed3e146101f95761009e565b806306fdde03146100a3578063095ea7b3146100c157806318160ddd146100f157806323b872dd1461010f578063313ce5671461013f575b600080fd5b6100ab610229565b6040516100b89190610ce0565b60405180910390f35b6100db60048036038101906100d69190610c00565b610262565b6040516100e89190610cc5565b60405180910390f35b6100f961045a565b6040516101069190610d22565b60405180910390f35b61012960048036038101906101249190610bb1565b610460565b6040516101369190610cc5565b60405180910390f35b6101476107d4565b6040516101549190610d3d565b60405180910390f35b6101656107d9565b6040516101729190610ce0565b60405180910390f35b61019560048036038101906101909190610b4c565b610812565b6040516101a29190610d22565b60405180910390f35b6101b361082a565b6040516101c09190610ce0565b60405180910390f35b6101e360048036038101906101de9190610c00565b610863565b6040516101f09190610cc5565b60405180910390f35b610213600480360381019061020e9190610b75565b610a5e565b6040516102209190610d22565b60405180910390f35b6040518060400160405280600881526020017f515243205445535400000000000000000000000000000000000000000000000081525081565b600082600073ffffffffffffffffffffffffffffffffffffffff168173ffffffffffffffffffffffffffffffffffffffff1614156102d5576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016102cc90610d02565b60405180910390fd5b600083148061036057506000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002054145b61036957600080fd5b82600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020819055508373ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167f8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925856040516104479190610d22565b60405180910390a3600191505092915050565b60005481565b600083600073ffffffffffffffffffffffffffffffffffffffff168173ffffffffffffffffffffffffffffffffffffffff1614156104d3576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016104ca90610d02565b60405180910390fd5b83600073ffffffffffffffffffffffffffffffffffffffff168173ffffffffffffffffffffffffffffffffffffffff161415610544576040517f08c379a000000000000000000000000000000000000000000000000000000000815260040161053b90610d02565b60405180910390fd5b6105ca600260008873ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205485610a83565b600260008873ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002081905550610693600160008873ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205485610a83565b600160008873ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000208190555061071f600160008773ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205485610ad0565b600160008773ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020819055508473ffffffffffffffffffffffffffffffffffffffff168673ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef866040516107bf9190610d22565b60405180910390a36001925050509392505050565b600881565b6040518060400160405280600981526020017f546f6b656e20302e31000000000000000000000000000000000000000000000081525081565b60016020528060005260406000206000915090505481565b6040518060400160405280600381526020017f515443000000000000000000000000000000000000000000000000000000000081525081565b600082600073ffffffffffffffffffffffffffffffffffffffff168173ffffffffffffffffffffffffffffffffffffffff1614156108d6576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016108cd90610d02565b60405180910390fd5b61091f600160003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205484610a83565b600160003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020819055506109ab600160008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205484610ad0565b600160008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020819055508373ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef85604051610a4b9190610d22565b60405180910390a3600191505092915050565b6002602052816000526040600020602052806000526040600020600091509150505481565b600081831015610abc577f4e487b7100000000000000000000000000000000000000000000000000000000600052600160045260246000fd5b8183610ac89190610dca565b905092915050565b6000808284610adf9190610d74565b905083811015610b18577f4e487b7100000000000000000000000000000000000000000000000000000000600052600160045260246000fd5b8091505092915050565b600081359050610b3181610eef565b92915050565b600081359050610b4681610f06565b92915050565b600060208284031215610b5e57600080fd5b6000610b6c84828501610b22565b91505092915050565b60008060408385031215610b8857600080fd5b6000610b9685828601610b22565b9250506020610ba785828601610b22565b9150509250929050565b600080600060608486031215610bc657600080fd5b6000610bd486828701610b22565b9350506020610be586828701610b22565b9250506040610bf686828701610b37565b9150509250925092565b60008060408385031215610c1357600080fd5b6000610c2185828601610b22565b9250506020610c3285828601610b37565b9150509250929050565b610c4581610e10565b82525050565b6000610c5682610d58565b610c608185610d63565b9350610c70818560208601610e53565b610c7981610eb5565b840191505092915050565b6000610c91600f83610d63565b9150610c9c82610ec6565b602082019050919050565b610cb081610e3c565b82525050565b610cbf81610e46565b82525050565b6000602082019050610cda6000830184610c3c565b92915050565b60006020820190508181036000830152610cfa8184610c4b565b905092915050565b60006020820190508181036000830152610d1b81610c84565b9050919050565b6000602082019050610d376000830184610ca7565b92915050565b6000602082019050610d526000830184610cb6565b92915050565b600081519050919050565b600082825260208201905092915050565b6000610d7f82610e3c565b9150610d8a83610e3c565b9250827fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff03821115610dbf57610dbe610e86565b5b828201905092915050565b6000610dd582610e3c565b9150610de083610e3c565b925082821015610df357610df2610e86565b5b828203905092915050565b6000610e0982610e1c565b9050919050565b60008115159050919050565b600073ffffffffffffffffffffffffffffffffffffffff82169050919050565b6000819050919050565b600060ff82169050919050565b60005b83811015610e71578082015181840152602081019050610e56565b83811115610e80576000848401525b50505050565b7f4e487b7100000000000000000000000000000000000000000000000000000000600052601160045260246000fd5b6000601f19601f8301169050919050565b7f41646472657373206973204e554c4c0000000000000000000000000000000000600082015250565b610ef881610dfe565b8114610f0357600080fd5b50565b610f0f81610e3c565b8114610f1a57600080fd5b5056fea264697066735822122015838477bf1095385aa7e44f2baf5c344b6d05f0f9a68899faa0749392c719ab64736f6c63430008020033"

alveyd -regtest -logevents -daemon -txindex
sleep 1
alvey-cli -regtest importprivkey $ARC20_SENDER_PRIVKEY
alvey-cli -regtest generatetoaddress 2500 $ARC20_SENDER
ARC20_CONTRACT=$(alvey-cli -regtest createcontract $ARC20_EXAMPLE_CONTRACT_BIN | jq -r ".address")
alvey-cli -regtest generatetoaddress 1 $ARC20_SENDER
echo "Deployed the example ARC20 contract at $ARC20_CONTRACT"
```



##### Checking balances by calling "balanceOf(address)"

To check the current token balance of an address for a ARC20 contract we need to execute the **balanceOf** method of the ARC20 contract. This execution should be performed as a read-only operation, which means we should use the **callcontract** RPC. An example is displayed below of how to check the balance of a particular address. The output is in hex, padded to 32 bytes, and should be divided by the number of **decimals** used in the contract.

```
ADDRESS="qauZFnmbNBNuY2ujQateDwzvL6zoxBiY3H"
ADDRESS_HEX=$(alvey-cli -regtest gethexaddress $ADDRESS)
CALLDATA="70a08231000000000000000000000000$ADDRESS_HEX"
alvey-cli -regtest callcontract $ARC20_CONTRACT $CALLDATA | jq -r ".executionResult.output" | python3 -c "print(int(input(), 16))")
```



##### Checking the constants of a ARC20 contract (decimals, name, standard, totalSupply, symbol)

To fetch the constants for a ARC20 contract, we need to execute the various methods of the ARC20 contract. These executions should be performed as read-only operations, which means we should use the **callcontract** RPC. An example is displayed below of how to check the various constants of a ARC20 contract.

```
ARC20_DECIMALS=$(alvey-cli -regtest callcontract $ARC20_CONTRACT 313ce567 | jq -r ".executionResult.output" | python3 -c "print(int(input(), 16))")
ARC20_NAME=$(alvey-cli -regtest callcontract $ARC20_CONTRACT 06fdde03 | jq -r ".executionResult.output" | xxd -r -p)
ARC20_SYMBOL=$(alvey-cli -regtest callcontract $ARC20_CONTRACT 95d89b41 | jq -r ".executionResult.output" | xxd -r -p)
ARC20_TOTALSUPPLY=$(alvey-cli -regtest callcontract $ARC20_CONTRACT 18160ddd | jq -r ".executionResult.output" | python3 -c "print(int(input(), 16))")

echo "decimals $ARC20_DECIMALS"
echo "name $ARC20_NAME"
echo "symbol $ARC20_SYMBOL"
echo "totalSupply $ARC20_TOTALSUPPLY"
```



##### Watching for new Transfer events using alveyd

The example below will watch for new Transfer events emitted by a ARC20 contract indefinitely. The example does this by continuously calling the **waitforlogs** RPC. The **waitforlogs** RPC will block until new events matching the call's arguments are seen. When it has seen events emitted by the contract it returns a list of entries that we can iterate through to fetch the details of one or more events that were emitted. In the example below we watch for all transfer events emitted by a contract. Note: the alveyd node this runs against must be started with the -logevents argument.

```
# This would be run against a live node listening for new contract events forever
# Note that alveyd should be started with -logevents to enable event logging
MIN_CONFIRMATIONS=20
LAST_BLOCK=$(alvey-cli -regtest getblockcount)
TOPIC="ddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef"
while true
do
    RESULTS=$(alvey-cli -regtest waitforlogs $LAST_BLOCK null "{\"addresses\": [\"$ARC20_CONTRACT\"], \"topics\": [\"$TOPIC\"]}" $MIN_CONFIRMATIONS)
    # Extract the events from the $RESULTS from .entries[topics]
    for ENTRY in $(echo $RESULTS | jq -c ".entries[]")
    do
        TXID=$(echo $ENTRY | jq -r ".transactionHash")
        SENDER_HEX=$(echo $ENTRY | jq -r ".topics[1]")
        RECEIVER_HEX=$(echo $ENTRY | jq -r ".topics[2]")
        AMOUNT_HEX=$(echo $ENTRY | jq -r ".data")

        SENDER_ADDRESS=$(alvey-cli -regtest fromhexaddress ${SENDER_HEX:24:40})
        RECEIVER_ADDRESS=$(alvey-cli -regtest fromhexaddress ${RECEIVER_HEX:24:40})
        AMOUNT_TRANSFERRED=$(python3 -c "print(0x$AMOUNT_HEX)")
        echo "TXID: $TXID"
        echo "SENDER: $SENDER_ADDRESS"
        echo "RECEIVER: $RECEIVER_ADDRESS"
        echo "AMOUNT: $AMOUNT_TRANSFERRED"
        echo ""
    done
    LAST_BLOCK=$(echo $RESULTS | jq -r ".nextblock")
done
```



##### Example of ARC20 transfer using an online alveyd node for signing

The easiest way to do a transfer between different addresses for a ARC20 contract is to simply use the **sendtocontract** RPC. 

```
ARC20_SENDER="qauZFnmbNBNuY2ujQateDwzvL6zoxBiY3H"
ARC20_RECEIVER="qL1ah15xwmxLL75TBxfwiXpoovn6dKV72h"
ARC20_TOKEN_TRANSFER_VALUE=1.1
ARC20_DECIMALS=8

# Adjust the tokens to transfer by the decimals and store the result in hex padded to 32 bytes
ARC20_TOKEN_TRANSFER_VALUE_PADDED_HEX=$(python3 -c "print(hex(int(${ARC20_TOKEN_TRANSFER_VALUE} * 10**0x${ARC20_DECIMALS}))[2:].zfill(64))")

# Convert the base58 receiver address into its 20-byte hex-encoded equivalents
ARC20_RECEIVER_HEX=$(alvey-cli -regtest gethexaddress $ARC20_RECEIVER)

# Pad the receiver hex to 32 bytes
ARC20_RECEIVER_PADDED_HEX="000000000000000000000000${ARC20_RECEIVER_HEX}"

# The function hash of "transfer(address,uint256)" concatenated with the receiver address and number of tokens to transfer
CALLDATA="a9059cbb${ARC20_RECEIVER_PADDED_HEX}${ARC20_TOKEN_TRANSFER_VALUE_PADDED_HEX}"

# Create, sign and broadcast the transfer
alvey-cli -regtest sendtocontract $ARC20_CONTRACT $CALLDATA 0 100000 0.0000004 $ARC20_SENDER
```



#### In depth on Alvey transactions

Alvey uses Bitcoin-style transactions. The transaction format is basically identical to Bitcoin transactions with a few modifications to the Bitcoin scripting engine. Alvey introduces 4 new opcodes in the scripting engine to allow for interaction with the Alvey EVM. The only relevant opcode for this document is OP_CALL (0xc2). 

##### OP_CALL

The OP_CALL opcode allows transaction output scriptPubKeys to be used to execute smart contracts in the Alvey EVM. The format for an OP_CALL scripts is the following:

version gaslimit gasprice calldata contractaddress OP_CALL



| field name      | size in bytes | typical value | description                                                  |
| --------------- | ------------- | ------------- | ------------------------------------------------------------ |
| version         | 1             | 0x04          | The version of the script, will always be 0x04 for purposes of this document. |
| gaslimit        | variable      | 200000        | The gas limit of the EVM execution, should be the same as the same limit in Ethereum for the purposes of this document. |
| gasprice        | variable      | 40            | The price per unit of gas, should typically be set to 40 as that is the current minimum limit for the gas price. Identical to the same concept in Ethereum. |
| calldata        | variable      | -             | The ABI encoded calldata, the same as in Ethereum, i.e., typically a 4-byte function signature followed by the ABI encoded function arguments. Identical to the same concept in Ethereum. |
| contractaddress | 20            | -             | The address of the contract you wish to execute.             |
| OP_CALL         | opcode        | 0xc2          | The OP_CALL opcode.                                          |

The script must follow the same rules for encoding as a typical bitcoin script, e.g., each value that is not an opcode will be prefixed with a push length unless it is a "smallint" (typically any bitcoin library used for creating a transaction will handle these details). Below is a transaction that contains an output that uses an OP_CALL scriptPubKey to execute a contract in Alvey.



##### Transaction Signing

The process of signing a raw transaction in Alvey is identical to signing a Bitcoin transaction. Typically, a raw transaction is signed using the signrawtransactionwithkey or signrawtransactionwithwallet in alveyd. Also, any Bitcoin library should be possible to be used to sign raw Alvey transactions.



##### Example of a raw ARC20 transfer using a Bitcoin library

Described below is the process of creating and signing a raw ARC20 transfer transaction using the bitcoin-js library.

```
var bitcoin = require('bitcoinjs-lib');
var testnet = bitcoin.networks.testnet;

/*
	Example of transaction that creates and signs a ARC20 transfer transaction for Alvey using the bitcoin-js library.
	The transaction format is nearly identical to that of Bitcoin, except for the OP_CALL output.
	Note, since OP_CALL is a Alvey specific opcode, it needs to be hardcoded as 0xc2
	The script below will assume that the input tx being spent is a P2PKH output.
*/

// The input tx hash and vout of the utxo we are going to spend
const TXINHASH = "e06d87ecfc8563e899118de9b9fc9deafc9aba41f2c420075bbf2c610fe160fc";
const TXINVOUT = 0;
// The WIF key of the input key. Alvey uses the same WIF format and prefx as Bitcoin
const TXINKEY = bitcoin.ECPair.fromWIF('cVNrcCHX5q318dDkpMFoLD1ack7TybxEjFB44xCsv5sW35kM7QuE', testnet);
// The value of the utxo, Alvey uses the same number of decimals as Bitcoin (8 decimals)
const TXINVALUE = 2000000000000;

// The contract address we wish to call
const ARC20_CONTRACT = "a20ee8612b8d338c55dcd03e65544339efd7cebc";

// The receiver address. Note that the SENDER is the address of the UTXO we are spending.
const ARC20_RECEIVER="qauZFnmbNBNuY2ujQateDwzvL6zoxBiY3H";

// The number of tokens we want to transfer
const ARC20_TOKEN_TRANSFER_VALUE=1n;

// The number of decimals defined by the ARC20 contract.
const ARC20_DECIMALS=8n;

// The minimum gas price is 0.00000040 Alvey, so we set the gas price to that here
const GASPRICE = 40;
// The gas schedule of Alvey is virtually identical to that of Ethereum, so a value that works for Ethereum will typically work for Alvey
const GASLIMIT = 100000;
// The OP_CALL (0xc2) opcode is Alvey specific and must therefore be hardcoded.
const OP_CALL = 0xc2;


var arc20_receiver_hex=bitcoin.address.fromBase58Check(ARC20_RECEIVER)['hash'].toString('hex')
var arc20_token_transfer_value_adjusted=ARC20_TOKEN_TRANSFER_VALUE * (10n**ARC20_DECIMALS);
var arc20_token_transfer_value_adjusted_hex=("0".repeat(64) + arc20_token_transfer_value_adjusted.toString(16)).slice(-64)

// The calldata, should be ABI encoded according the Ethereum ABI specification.
var calldata = "a9059cbb000000000000000000000000"+arc20_receiver_hex+arc20_token_transfer_value_adjusted_hex;

// The input value minus the gas cost/txfee
var changeValue = TXINVALUE-(GASPRICE*GASLIMIT);

// The opcall scriptPubKey on the form of "version gaslimit gasprice calldata contractaddress OP_CALL"
var contractCallScript = bitcoin.script.compile([
	bitcoin.script.number.encode(4), // version
	bitcoin.script.number.encode(GASLIMIT), // gas limit
	bitcoin.script.number.encode(GASPRICE), // gas price
	new Buffer(calldata, "hex"),  // The ABI encoded calldata
	new Buffer(ARC20_CONTRACT, "hex"), // the contract's address
	OP_CALL // OP_CALL opcode
]);

// A normal P2PKH change output
var changeScript = bitcoin.script.compile([
	bitcoin.opcodes.OP_DUP,
	bitcoin.opcodes.OP_HASH160,
	new Buffer(bitcoin.crypto.hash160(TXINKEY.publicKey)),
	bitcoin.opcodes.OP_EQUALVERIFY,
	bitcoin.opcodes.OP_CHECKSIG
]);

var txb = new bitcoin.TransactionBuilder(testnet);
txb.addInput(TXINHASH, TXINVOUT);
txb.addOutput(contractCallScript, 0);
txb.addOutput(changeScript, changeValue);
txb.sign(0, TXINKEY);
var tx = txb.build();
console.log(tx.toHex());
```



##### Notes

* The first input to the transaction must be the sender (unless you use OP_SENDER style scripts, which is not covered in this document)

* If you use 
* Any Bitcoin library should be possible to use for signing, as long as it is possible to manually encode the OP_CALL opcode in the script.



#### Appendix

##### ARC20 Function signatures

```
dd62ed3e: allowance(address,address)
095ea7b3: approve(address,uint256)
70a08231: balanceOf(address)
313ce567: decimals()
06fdde03: name()
95d89b41: symbol()
18160ddd: totalSupply()
a9059cbb: transfer(address,uint256)
23b872dd: transferFrom(address,address,uint256)
```

#
#  A Client-Server Model for Bitcoin (DRAFT)

### Purpose
The purpose of this document is to look at Bitcoin Nodes and applications through a client-server paradigm where the server is a "wrapper" around a Bitcoin Node and the client is an SDK which provides the programatic primitives for consuming applications to interact with the server. (Think about this as similar to an AWS service and client SDK, but in the case of Bitcoin, the server is run by the users of the client application and is not a centralized service run by a third party). Later in this document, we explore the anatomy of a Bitcoin Server and Bitcoin Client and look at some common implementations. 

For more context on how and why Bitcoin Applications needs to connect and interact with a Bitcoin Node, see the [writeup on connecting to a bitcoin node](https://github.com/johnyukon21/johnyukon21.github.io/blob/master/connecting_to_a_bitcoin_node.md)

We also look at how the industry can move to towards an open standard for the client server interaction with the goal of 

1. Enabling different client and server implementations to be able to inter-operate. eg. have Green wallet connect to a Casa Node. This will lead to a less balkanized eco-system that will ultimately give users more options in mixing and matching servers and clients and hopefully increase the number of users running fully validating nodes. 

2. Have standard clients with multiple language bindings that developers can add a dependency to, and start interacting with Bitcoin (quick developer onboarding). 

### The Anatomy of a Bitcoin Server
A *Bitcoin Server* is defined as an application that runs alongside a Bitcoin Node and exposes the blockchain data on the node through APIs accessible through the network. Some features that a Bitcoin Server should provide are:

* AuthN and AuthZ - the server should only process authenticated and authorized requests.
* DDOS protection - given that the server endpoint might be exposed on the public Internet, DDOS protections should be in place.
* Encrypted communication/TOR - communication between the client and the server should be encrypted. 
* Secure/Hardened APIs - the exposes APIs should be hardened to the point that there are protections against malicious clients.
* Blockchain Data Indexing - depending on the usecases that the server is supporting (single xpub, multiple xpub, block explorer) it needs different degrees of indexing. 
* Inbound connectivity on the internet/TOR - the node should be able to accept inbound connections on the public internet.

#### Implementations
The following are the common implementations of a Bitcoin Server:

* [electrum](https://electrum.org/#home) - there are multiple implementations of the electrum server that implements the Stratum protocol. Clients to can connect to electrum servers and access the underlying Bitcoin Node. 
  * [electrum-server](https://github.com/spesmilo/electrum-server) 
  * [electrumx](https://github.com/kyuupichan/electrumx/)
  * [electrum-personal-server](https://github.com/chris-belcher/electrum-personal-server)
  * [electrs](https://github.com/romanz/electrs)
  
* [libbitcoin](https://libbitcoin.org) - libbitcoin in a full re-implementation of the consensus rules but also comes built in with a Bitcoin Server that "exposes a custom query TCP API built based on the ZeroMQ networking stack. It supports server, and optionally client, identity certificates and wire encryption via CurveZMQ and the Sodium cryptographic library and supports simple and advanced scenarios, including stealth payment queries." 

* [bitcore](https://github.com/bitpay/bitcore) - Bitcore is a similar wrapper service around the Bitcoin Core Node that provides REST APIs that clients can call. 

#### Hardware Nodes
There are a number of plug and play devices that come with different types of Bitcoin Servers installed.

* [NODL](http://nodl.it) - a Bitcoin and Lightning node that runs several services including an Electrum Server. 
* [BitBox Base](https://github.com/digitalbitbox/bitbox-base) - a Bitcoin and Lightning node that run the rust implementation of the Electrum Server
* [Casa Node](https://keys.casa/lightning-bitcoin-node/) - a Bitcoin and Lightning node that uses a custom protocol (TODO: confirm this) so that Casa mobile keymaster can connect to it. 
* [Dojo](https://samouraiwallet.com/dojo) - Bitcoin Node that uses a custom protocol for the Samourai wallet to connect to. 
* [Bitseed](https://bitseed.org/product/bitseed-3/) - TODO (bitseed website is down)

### The Anatomy of a Bitcoin Client
A *Bitcoin Client* is defined as a software library that provides the programatic primitives for a consuming application to connect to and interact with a *Bitcoin Server*. Some features that a Bitcoin Client should provide are:

1. Key Generation and Management Utilities ("wallet")
2. P2P/Serialization/Deserialization, Mempool interaction
3. Node Communication: JSON-RPC over some overlay protocol like Stratum

#### Implementations 
* [libbitcoin-client](https://libbitcoin.org) - client to call the libbitcoin-server. 
* [node-electrum-client](https://github.com/you21979/node-electrum-client)
* [electrum](https://github.com/spesmilo/electrum)
* [rust-electrumx-client](https://github.com/evgeniy-scherbina/rust-electrumx-client) 
* [electrum-wallet-chrome-extension](https://github.com/anfedorov/electrum-wallet-chrome-extension) 
* [electrum-btx](https://github.com/LIMXTEC/electrum-btx) 
* [electrumjs](https://github.com/akshatmittal/electrumjs)
* [bitcore-wallet-client](https://github.com/bitpay/bitcore/tree/master/packages/bitcore-wallet-client) - client for calling the bitcore wallet services. 

### Moving towards an inter-operable client server standard
As seen above, many of the current server implementations follow their own custom standard/protocol for the client-server interaction. There might an opportunity for the industry to move towards an open Bitcoin Server standard where clients and servers can interoperate across implementations. Stratum seems to be in the best position (the only one?) to be adopted as that open standard. 

### What needs to be done?
*  Investigate if Stratum the best open standard for everyone to adopt? What are its limitations? Do we need to add new APIs?
* Multiple language/platform bindings for the clients
* API documentation, tooling around server deployment across different compute form factors (plug and play node, docker, virtual compute, bare metal). 

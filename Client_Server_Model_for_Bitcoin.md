#  A Client-Server Model for Bitcoin (DRAFT)

### Purpose
The purpose of this document is to look at Bitcoin Nodes and applications through a client-server paradigm where the server is a "wrapper" around a Bitcoin Core Node and the client is an SDK which provides the programatic primitives for consuming applications to interact with the server. (Think about this as similar to an AWS service and client SDK, but in the case of Bitcoin, the server is run by the users of the client application and is not a centralized service run by a third party). Another way to look at this is as an *open standard for securely "remote accessing" a Bitcoin Core Node over the public internet*. This will eliminate the need for the node operator to have to worry about port forwarding, ssh tunneling, VPN setup etc to make the node available for remote connection. For the purposes of this document, this open standard is going to be called the *RemoteNode* protocol. A *RemoteNode* server is an application that will expose controlled access to a Bitcoin Node and a *RemoteNode* client will provide programatic primitives to connect and talk to the the server.


### Goals
The goals of the the *RemoteNode* standard are:

1. Eliminate the need for node operators having to deal with ssh tunneling, VPN, port forwarding etc which are all different strategies that are used today to enable remote connection. Instead, the *RemoteNode* protocol will provide a standard pre-configured way to make a node available for remote apps. 

2. Have standard clients with multiple language bindings that developers can add a dependency to, and start interacting with Bitcoin (quick developer on-boarding). 

3. Providing a well documented and implemented open standard that if adopted by the community, will enable different client and server implementations to be able to inter-operate. eg. have Green wallet connect to a Casa Node. This will lead to a less balkanized eco-system that will ultimately give users more options in mixing and matching servers and clients and possibly increase the number of users running fully validating nodes. 

### Vision
In the fullness of time, the vision that this idea hopes to drive is as follows:

Bob buys a device that runs a bitcoin node alongside a *RemoteNode* server (something that looks like an Echo Show or Google Home Hub). The device arrives in the mail, Bob plugs it in and connects it to Wifi using the touchscreen. The devices starts syncing the blockchain and once its complete it presents a QR code for connecting to the server via TOR. Bob takes his phone out, installs any wallet that supports the *RemoteNode* standard and scans the QR code to connect to his own node. Bob is now a sovereign Bitcoin user. 

Alice is Bob's friend who he got into Bitcoin. Alice trusts Bob and asks Bob if she can use his node. Bob shares a link with Alice that she can use to connect to Bob's node till she gets her own at which point Bob can revoke her access. 

Alice, being a developer, wants to build her own app/wallet that can connect to a node using the *RemoteNode* protocol. She adds one of the client libraries to her development environment and can start coding right away calling the APIs that the client library exposes to connect to an available backend server. 

### The Anatomy of a *RemoteNode* Server
A *RemoteNode* server is defined as an application that runs alongside a Bitcoin Node and exposes the blockchain data on the node through APIs accessible through the network. Some features that a *RemoteNode* server should provide are:

* AuthN and AuthZ - the server should only process authenticated and authorized requests.
* DDOS protection - given that the server endpoint might be exposed on the public Internet, DDOS protections should be in place.
* Encrypted communication/TOR - communication between the client and the server should be encrypted. 
* Secure/Hardened/Standardized APIs - the exposed APIs should be hardened to the point that there are protections against malicious clients. 
* Blockchain Data Indexing - depending on the usecases that the server is supporting (single xpub, multiple xpubs, block explorer) it needs different degrees and types of indexing. 
* Inbound route-ablity on the internet/TOR - the node should be able to accept inbound connections on the public internet.
* Network communication protocol  - the APIs exposed by the server should be callable in a pre-defined protocol (eg REST, Stratum)

NOTE: The *RemoteNode* server is not a re-implementation of the consensus rules, it is simply a way to enable remote applications to interact with a node over the public internet. That said, it is also important to keep in mind that there are alternative implementations of the consensus rules that come with its own server built in. eg. libbitcoin, bcoin, btcd

#### Implementations
The following are the currently available common implementations that provide functionality similar to the concept of a *RemoteNode* server:

* [electrum](https://electrum.org/#home) - there are multiple implementations of the electrum server that implements the Stratum protocol. Clients to can connect to electrum servers and access the underlying Bitcoin Node. 
  * [electrum-server](https://github.com/spesmilo/electrum-server) 
  * [electrumx](https://github.com/kyuupichan/electrumx/)
  * [electrum-personal-server](https://github.com/chris-belcher/electrum-personal-server)
  * [electrs](https://github.com/romanz/electrs)

* [bitcore](https://github.com/bitpay/bitcore) - Bitcore is a similar wrapper service around the Bitcoin Core Node that provides REST APIs that clients can call. 

The following are the current alternative implementations of the consensus rules, that come with a built in server:

* [libbitcoin](https://libbitcoin.org) - libbitcoin in a full re-implementation of the consensus rules but also comes built in with a Bitcoin Server that "exposes a custom query TCP API built based on the ZeroMQ networking stack. It supports server, and optionally client, identity certificates and wire encryption via CurveZMQ and the Sodium cryptographic library and supports simple and advanced scenarios, including stealth payment queries." 
* [bcoin](https://bcoin.io/) - an alternative implementation of the bitcoin protocol, written in node.js which comes with a an HTTP server that listens on the standard RPC port. It exposes a REST JSON, as well as a JSON-RPC api.
* [btcd](https://github.com/btcsuite/btcd) - btcd is an alternative full node bitcoin implementation written in Go that comes with a TLS-enabled JSON-RPC interface that provides access to the APIs through both HTTP POST requests and Websockets.

#### Hardware Nodes
There are a number of plug and play devices that come with different types of Bitcoin Servers installed.

* [NODL](http://nodl.it) - a Bitcoin and Lightning node that runs several services including an Electrum Server. 
* [BitBox Base](https://github.com/digitalbitbox/bitbox-base) - a Bitcoin and Lightning node that run the rust implementation of the Electrum Server
* [Casa Node](https://keys.casa/lightning-bitcoin-node/) - a Bitcoin and Lightning node that uses a custom protocol (TODO: confirm this) so that Casa mobile keymaster can connect to it. 
* [Dojo](https://samouraiwallet.com/dojo) - Bitcoin Node that uses a custom protocol for the Samourai wallet to connect to. 
* [Bitseed](https://bitseed.org/product/bitseed-3/) - TODO (bitseed website is down)

### The Anatomy of a *RemoteNode* Client
A *RemoteNode* client is defined as a software library that provides the programatic primitives for a consuming application to connect to and interact with a *RemoteNode* server. The main function of the client is to provide APIs that a consuming program can use to remotely connect to the node. The client should also expose the programatic primitives required for the application to set up the node URL, authenticate itself to the node, sign requests etc. 

#### Implementations 
The following are the currently available common implementations that provide functionality similar to the concept of a *RemoteNode* client:

* [libbitcoin-client](https://libbitcoin.org) - client to call the libbitcoin-server. 
* various electrum clients: [node-electrum-client](https://github.com/you21979/node-electrum-client), [electrum](https://github.com/spesmilo/electrum), [rust-electrumx-client](https://github.com/evgeniy-scherbina/rust-electrumx-client) ,[electrum-wallet-chrome-extension](https://github.com/anfedorov/electrum-wallet-chrome-extension), [electrum-btx](https://github.com/LIMXTEC/electrum-btx), [electrumjs](https://github.com/akshatmittal/electrumjs)
* [bitcore-wallet-client](https://github.com/bitpay/bitcore/tree/master/packages/bitcore-wallet-client) - client for calling the bitcore wallet services. 

NOTE: if we step back and look at all the features a more general bitcoin client or application would need, there are other components like 1. Key Generation and Management Utilities ("wallet") 2. Transaction Creation/Signing and Broadcasting 3. P2P connection/Serialization/Deserialization, Mempool interaction 4. UTXO management etc. Those functions are complementary to the *RemoteNode* client and therefore out of scope of the *RemoteNode* client itself. However it is worth noting that there are various implementations of these functions available today like [rust-bitcoin](https://github.com/rust-bitcoin/rust-bitcoin), [nbitcoin](https://github.com/MetacoSA/NBitcoin), [libwally](https://github.com/ElementsProject/libwally-core) and [bitcoinj](https://bitcoinj.github.io). 

### Moving towards an inter-operable client server standard
As seen above, many of the current server implementations follow their own custom standard/protocol for the client-server interaction. There might an opportunity for the industry to move towards an open standard (*RemoteNode*) where clients and servers can interoperate across implementations. 

### What needs to be done?
* All aspects mentioned in the "The Anatomy of a *RemoteNode* Server" above should be standardized. What standards/implementations should be used as a starting point for each of them?
* Investigate if stratum is a good network communication protocol (used in electrum)? What are its limitations? Do we need to add new APIs? Does BetterHash work here or is that purely to be used in the context of mining pools?
* Multiple language/platform bindings for the clients
* API documentation, tooling around server deployment across different compute form factors (plug and play node, docker, virtual compute, bare metal). 

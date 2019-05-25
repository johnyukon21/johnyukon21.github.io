#  A Client-Server Model for Bitcoin (DRAFT)

### Purpose
The purpose of this document is to visualize Bitcoin through a client server paradigm where the server is a Bitcoin Node run sovereignly and the client is an application built using an SDK in whatever language the SDK is available in (similar to an AWS service and client SDK, but in the case of Bitcoin the client connects to the user's node and not a centralized server). We explore the anatomy of a Bitcoin Server and Bitcoin Client. 

For need of connection see other doc: 
Have an open standard for the APIs which
1. make development easy for client and server side
2. make fullnode deployment and usage easy and intiutive 

Note that this is not a new idea but just an attempt to provide some common verbage for building tools around this paradigm.

### The Anatomy of a Bitcoin Server

A *Bitcoin Server* is defined as an application that runs alongside a Bitcoin Node, that provides exposes the functions of the node through APIs accessible through the network. Some features that any Bitcoin Server should provide are:

* DDOS protection
* Authentication
* Encryption/TOR
* Secure APIs
* Indexing
* Disoverability/TOR

#### Implementations
Electrum
Libbitcoin

#### Hardware
* NODL
* Shift
* Casa - ?
* Dojo - ?
* Bitseed - ?

### The Anatomy of a Bitcoin Client

A *Bitcoin Client* is defined as a software library that provides the progamatic primitives for a consuming application to connect to and interact with a *Bitcoin Server*. 

1. Key Management Module ("wallet")
2. P2P/Serilization/Deserilization Module/mempool
3. Node communication: JSON-RPC/Stratum Module
4. Utilities

Cryptographic library
TCP/IP

#### Implementations 

### Moving towards an inter-operable client server standard
As seen above, many of the current server implementations follow their own standard/protocol. There might an opportunity to for the industry to move towards an open Bitcoin Server standard where clients and servers can interoperate across implementations. Stratum seems to be in the best position (the only one?) to be adopted as that open standard. There is work needed to be done in terms of client implementations in multiple languages, API documentation, tooling around server deployment across different compute form factors

### Auxilliary Thoughts
soverignty level
display in wallets whose node is being used (authenticty verified by: {random node|my node| company's node})

### Pie in the Sky Vision
A user buys a device like an echo show, plugs it in, connects it to wifi and the stats get presented on the screen with qr code for connection. 

IBD and indexing happens and its ready to use. Can come preloaded with UTXO (assumeUTXO)

They take their phone out and scan the code and you they are done. Sets up a lightning wallet as well as a simple cold storage and maybe accesss accounts through PLAID, and be able to buy. 

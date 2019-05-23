# Connecting to the Bitcoin Network (DRAFT)

This document explores the different ways in which an application can plug into the Bitcoin network. 

### Definitions:
For the purposes of this document, the terms "Bitcoin Node" and "Bitcoin Application" are defined as follows:

**Bitcoin Node ("node"):** a running instance of the [reference implementation of the consensus code](https://github.com/bitcoin/bitcoin) that talks to the Bitcoin peer to peer network to arrive at consensus with the rest  of the nodes on the state of the blockchain, index the blockchain data and exposes it to other systems/programs that can connect to the node and query it. 

**Bitcoin Application ("application"):** an application that connects to a Bitcoin node via some interface (more on this below) to read the blockchain data or broadcast transactions eg. a wallet. 

###  Plugging into Bitcoin:
Any application that needs to interact with Bitcoin will have to connect to a node in one of the following ways:
1. Connect to a dedicated endpoint for a bitcoin node that is run by the application user and trusted by the  user. 
2. Connect to a dedicated endpoint for a bitcoin node service that is run by a third party. eg Trezor web wallet connects to backend nodes run by Trezor 
3. Use an SPV scheme like [BIP37](https://github.com/bitcoin/bips/blob/master/bip-0037.mediawiki)(eg BreadWallet) or [BIP157](https://github.com/bitcoin/bips/blob/master/bip-0157.mediawiki)(eg Wasabi) to connect to random nodes on the Bitcoin P2P network.

The rest of this documents explores the different ways for an application to interact with a node specifically in the scenario where there is a trusted (user run), dedicated Bitcoin node. Although it is out of the scope of this document, it is worth noting that this is the most private and sovereign way to use a Bitcoin application and there is extensive literature available on the privacy and sovereignty concerns with using a node run by a third party or using the SPV scheme. Below we explore 3 commons ways to communicate to a Bitcoin node.

#### P2P
One of the interfaces into a Bitcoin node is the peer to peer port (usually 8333) which external applications can query using 
the [Bitcoin Peer to Peer protocol](https://en.bitcoin.it/wiki/Protocol_documentation). This interface is usually used by nodes to communicate to each other to propoagte blocks and transactions. Usually applications connect to the p2p interface only for broadcasting transactions. Other than that, this interface is not powerful enough to be used by applications like wallets or block explorers.

#### JSON-RPC
The Bitcoin node reference implementation exposes a [JSON-RPC interface](https://en.bitcoin.it/wiki/Original_Bitcoin_client/API_calls_list) (usually at port 8332) which applications can query. When an application runs on the same host as the Bitcoin node, it can talk to the JSON-RPC interface using the loopback address. 

There are however some considerations if the RPC interface is being exposed on the network for a remote application to call
into a Bitcoin node. Firstly, it is not safe to expose the RPC port on the internet as the data is transmitted without encryption on the wire and the interface does not have any DDOS protections. It is also important to note that if you are running a full node with the bitcoin-qt wallet, these RPC commands  essentially gives an interface into the wallet as well. 

If the RPC interface is being exposed only to specific instances of client applications one option might be to tunnel ["with SSH or stunnel which will provide a secure, authenticated path without exposing the socket any further than localhost"](https://en.bitcoin.it/wiki/Enabling_SSL_on_original_client_daemon) or to restrict the application and the node within in private network.

#### Overlay Protocol on JSON-RPC
To overcome the problems mentioned above with exposing the JSON-RPC interface over the network, a common solution is to use an
overlay protocol like stratum. The [stratum protocol](http://docs.electrum.org/en/latest/protocol.html) overlays on the JSON-RPC interface. [Electrum servers](https://en.bitcoin.it/wiki/Electrum#Server_software) implement the stratum
protocol and by connecting to the JSON-RPC interface of a full node running on the same host as the electrum server, via the loopback address, it exposes the blockchain data via the stratum protocol APIs for the application to connect to over the wire. So a user can run an electrum server along side a bitcoin node and make the node available for queries on the network and remote appications can call in. 

### Conclusion
For remote applications, using an overlay protocol like stratum seems to be the simplest and safest way to interact with a Bitcoin Node. In the next document, we will exploring more into how to build application that "speak" staratm and can connect to an electrum server. 

### Projects
[btc-cli](https://github.com/johnyukon21/btc-cli/tree/development) - an experimental client of talking to a node via p2p, RPC and stratum

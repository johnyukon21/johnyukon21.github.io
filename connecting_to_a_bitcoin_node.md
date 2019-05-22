# Plugging your Application to the Bitcoin Network

*WARNING:* THIS DOCUMENT IS STILL A DRAFT AND IS BEING REVIEWED. Please send any  feedback to johnyukon21 at protonmail dot com


### Context & Definitions:
This document assumes basic knowledge of the Bitcoin. There  are many  ways to define and describe Bitcoin, but for the purposes of this document and defining Bitcoin, Bitcoin Node and Bitcoin Application as follows:

**Bitcoin:** a peer to peer computer network that comes to consenus on  the state of the blockchain in a decentralized manner.

**Bitcoin Node ("node"):** an application running the consensus code to talk to the peer to peer network to arrive at consensus with the rest  of the nodes on the state of the blockchain and exposes the data on the blockchain to other systems/programs. Once a node on the network has the latest state of the blockchain, the data in the blockchain becomes the base data layer which any application plugging into the Bitcoin network will need access to. The data on the blockchain is indexed further to allow for quick queries instead of having to scan the blockchain for every query. 

**Bitcoin Application ("application"):** an application that connects to a Bitcoin node via some interface (more on this below) to read the blockchain data or broadcast transactions eg. a wallet. 

Any application needing access to the blockchain data will have to connect to a node in one of the following ways
1. connect to a dedicated endpoint for a bitcoin node that is run by the application user and trusted by the  user. 
2. connect to a dedicated endpoint for a bitcoin node service that is run by a third party. Many wallets run this way. 
3. use an SPV scheme like [BIP37](https://github.com/bitcoin/bips/blob/master/bip-0037.mediawiki) or [BIP157](https://github.com/bitcoin/bips/blob/master/bip-0157.mediawiki).

The rest of this documents explores the different ways for an application to interact with a node specifically case (1). 
Although it is out of the scope of this document, it is worth noting that this is the most private and soverign way to use
a Bitcoin application and there is extensive literature available on the cocerns with (2) and (3)

###  Plugging into Bitcoin:
#### peer to peer
One of the interfaces into a Bitcoin node is the peer 2 peer port (usually 8333) which external applications can query using 
the [Bitcoin Peer to Peer protocol](https://en.bitcoin.it/wiki/Protocol_documentation). This interface is usually used by nodes  to communicate to each other to propoagte blocks and transactions. Usually applications connect to the peer 2 peer interface only for broadcasting transactions. Other than that, this interface is not powerful enough  to be used by applications like wallets or block explorers.

#### JSON-RPC
The Bitcoin node reference implementation exposes a [JSON-RPC interface](https://en.bitcoin.it/wiki/Original_Bitcoin_client/API_calls_list) (usually at port 8332) which applications can query. When an application runs on the same host as the Bitcoin node, it can talk to the JSON-RPC interface using the loopback address. 

There are however some considerations if the RPC interface is being exposed on the network for a remote application to call
into a Bitcoin node. Firstly, it is not safe to expose the RPC port on the internet as the APIs are not hardended, data is 
transmitted without encryption on the wire and the interface does not have any DDOS protections. It is also important to note that  if you are running a full node with the bitcoin-qt wallet, these RPC commands  essentially gives an interface into the wallet as well. 

If the RPC interface is being exposed only to specific instances of client applications one option might be to tunnel ["with SSH or stunnel which will provide a secure, authenticated path without exposing the socket any further than localhost"](https://en.bitcoin.it/wiki/Enabling_SSL_on_original_client_daemon) or to restrict the  application and the node within in private network.

#### Overlay Protocol on JSON-RPC
To overcome the problems mentioned above with exposing the JSON-RPC interface over the network, a common solution is to use an
overlay protocol like stratum. The [stratum protocol](http://docs.electrum.org/en/latest/protocol.html) overlays on the JSON-RPC interface. [Electrum servers](https://en.bitcoin.it/wiki/Electrum#Server_software) implement the stratum
protocol and by connecting to the JSON-RPC interface of a full node via loopback, it exposes the blockchain data via the stratum protocol APIs for the application to connect to over the wire. 

### Conclusion
TODO

### Projects
https://github.com/johnyukon21/btc-cli/tree/development (an experimental client of talking to a node via p2p, RPC and stratum)
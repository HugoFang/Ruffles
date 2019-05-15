# Ruffles [![Build Status](https://img.shields.io/appveyor/ci/midlevel/ruffles/master.svg?logo=appveyor)](https://ci.appveyor.com/project/MidLevel/ruffles/branch/master)
Ruffles is a fully managed UDP library designed for high performance and low latency.

## Why another reliable UDP library?
There are many RUDP libraries such as ENET, Lidgren, LiteNetLib. While many of them are great, Ruffles aims to fill in one niche that is largely not filled, that being lightweight fully managed libraries.

To compare to the examples above, ENET is amazing and is pretty much what Ruffles want to be, but it's unmanaged. Lidgren, LiteNetLib and many other managed libraries can feel too bloated and contain many features that are unnecessary and they are often much slower.


## Features
Ruffles is still **WIP**, more features might come.

### Connection Challenge
The Ruffle protocol requires a challenge to be completed before a connection can be established. Currently, the challenge is a hashcash like challenge that is supplied by the server, brute force solved by the client and submitted. (Uses Fowler-Noll-Vo hash function instead of SHA1 currently).

### DOS Amplification Prevention
DOS amplification is prevented by requiring unproportional connection message sizes. In addition, because of the connection challenge it's not computationally feasible to attack on Layer 4.

### Slot Filling Prevention
Ruffles has a fixed amount of connection slots that can be used for pending connections, this ***limits*** the usability of slot filling attacks on Layer 4. Pending connections have a fixed timeout to solve the computationally expensive HashCash challenge before being knocked out, the slot will be available once again after that. It's planned to patch this with an initial HashCash based on a tight window which will cause "slot fillers" to not even get assigned a pending slot before solving a general purpose challenge. This will be done in conjunction with a hash set to make sure the initialization vector used by the HashCash is not reused. This means that an attacker will have to exhaust all CPU resources in order to bring the server down.

### Connection Management
Ruffles handles all connection management for you. It's a fully connection oriented protocol with heartbeat keepalive packets sent to ensure the connection is alive.

### High Performance
Ruffles is fully garbage free, this is accomplished with a custom memory allocator in GC space. This ensures no memory is leaked to the garbage collector unless for resizing purposes. This makes Ruffles blazing fast. It also avoids memory copies as much as possible.

### Reliability and Sequencing
There are currently a few ways of sending messages in Ruffles. The types are:
#### Reliable
All messages are guaranteed to be delivered, the order is not guaranteed, duplicates are dropped. Uses a fixed sliding window.
#### ReliableSequenced
All messages are guaranteed to be delivered with the order also being guaranteed, duplicates are dropped. Uses a fixed sliding window.
#### Unreliable
Delivery is not guaranteed, nor is the order. Duplicates are dropped.
#### UnreliableSequenced
Delivery is not guaranteed but the order is. Older packets and duplicate packets are dropped.
#### UnreliableRaw
Delivery is not guaranteed nor is the order. Duplicates are not dropped.

### Threading
Ruffles can run in many different threading environments, it can be run passively single threaded, actively single threaded, or in a threaded environment where everything is done via message queues while remaining garbage free.

### Dependency Free
Ruffles is 100% dependency free, it's thus very portable and should run on most platforms.

### IPv6 Dual Mode
Ruffles supports IPv6 dual socket mode. It does this by using two sockets bound to the same port, thus accomplishing full dual stack functionality that is invisible to the user.

### Small Packet Merging
Small packets will be delayed for sending, this allows them to be merged into one larger packet. This can be disabled and enabled on a per packet basis. The delay and max merge size can also be configured.


## Roadmap
This is stuff I want to and plan to add

* Path MTU
* Fragmentation
* Explicit Nack
* Layer 4 DOS Prevention with an on-connect HashCash challenge
* Reliable StateUpdate / LastPacket channel
* MLAPI.Relay Support
* MLAPI.NAT (Holepuncher) support
* Bloatless Moduled Library (Make all the garbage features like relay support separate modules to keep the core library bloat free and small)

## Maybe Roadmap
Here are the features that are considered but not decided. This is to prevent bloat.
* Multicasting
* Meshing / Peer relaying
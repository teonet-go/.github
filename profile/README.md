# Teonet v5

Teonet v5 is pure Golang implementation of Trudp and Teonet librarys and Teonet applications

![Teont Network](https://github.com/teonet-go/.github/blob/main/profile/network.png?raw=true)

Teonet is communication Go package to create network / cloud transport. Teonet uses UDP to communicate between its network peers. There UDP packets are encrypted with unique keys. Teonet uses own UDP based protocol called Teonet Reliable UDP (TRU) for real time communications that allows sending messages with low latency and provides protocol reliability features.

## The main Teonet capabilities

- P2P: In main network schema all Teonet peers are connected point to point. The communication between peers are going without central server. We need only one peer with dedicated IP called rauth to connect peers to each other.
- Address: The peers has its own names (text coded name) which is exclusive Teonet network address.
- Messages: The main users communication protocol is message. The message can be send to any peer by its name and can has any length and format.
- Reliability: All messages are reliable.

## Networking with Teonet

It may be done any number of Teonet networks. Each Teonet network has it's own monitor. The messages from one network can be send to another network if you know it unique address.

## Get started

Learn how to start building software with Teonet.

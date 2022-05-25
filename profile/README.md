# Teonet v5

Teonet v5 is pure Golang implementation of Trudp and Teonet libraries and Teonet applications

![Teont Network](https://github.com/teonet-go/.github/blob/main/profile/network.png?raw=true)

Teonet is designed to create client-server systems and build networks for server applications operating within a microservice architecture. To do this, Teonet creates a network / cloud transport between its members. This transport uses UDP for communication between network peers. UDP packets are encrypted with unique keys. Teonet uses its own UDP-based protocol called Teonet Reliable UDP (TRU) for real-time communication, which allows low latency messages to be sent and protocol reliability features.

## The main Teonet capabilities

- P2P: In main network schema all Teonet peers are connected point to point. The communication between peers are going without central server. We need only one peer with dedicated IP called rauth to connect peers to each other;
- Address: The peers has its own names (text coded name) which is exclusive Teonet network address;
- Messages: The main users communication protocol is message. The message can be send to any peer by its name and can has any length and format;
- Command mode: Messages can has digital number of command;
- API: Teonet API included command name, command number, command descriptions and description of input and output parameter;
- Monitor: Teonet monior allow get application realtime statistic;
- Deploy: Teonet deploy system installs and mange Teonet applications on its hosts;
- Reliability: All messages are reliable.

## Networking with Teonet

It may be done any number of Teonet networks. Each Teonet network has it's own monitor. The messages from one network can be send to another network if you know it unique address.

## Get started

To learn how to start building software with Teonet use the
[>> Simple message mode](profile/simple.md#simple-message-mode) example.

## Table of Contents

- [Teonet description](#teonet-v5)
- [Simple message mode](profile/simple.md#simple-message-mode)
  - [Simple Clent](profile/simple.md#teonet-simple-client-application)
  - [Simple Server](profile/simple.md#teonet-simple-server-application)
- [Command message mode](profile/command.md#command-message-mode)
  - [Command mode Clent](profile/command.md#teonet-command-mode-client-application)
  - [Command mode Server](profile/command.md#teonet-command-mode-server-application)
- [API message mode](profile/api.md#api-message-mode)
  - [API Clent](profile/api.md#teonet-api-mode-client-application)
  - [API Server](profile/api.md#teonet-api-mode-server-application)

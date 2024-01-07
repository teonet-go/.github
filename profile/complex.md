# Complex Teonet Example

In this example we create several [Teonet](https://github.com/teonet-go) microservices to show how to create client-server applications using Teonet Microserive Applications.

<p align="center">
<img src="https://github.com/teonet-go/.github/blob/main/profile/microservices.jpg?raw=true" />
</p>

## Microservice Description

Microservices are an architectural and organizational approach to software development where software is composed of small independent services that communicate over well-defined APIs. These services are owned by small, self-contained teams.

Microservices architectures make applications easier to scale and faster to develop, enabling innovation and accelerating time-to-market for new features.

[Wikipedia](https://en.wikipedia.org/wiki/Microservices)

## Client-Server Description

Client-Server model is a distributed application structure that partitions tasks or workloads between the providers of a resource or service, called servers, and service requesters, called clients.Often clients and servers communicate over a computer network on separate hardware, but both client and server may reside in the same system. A server host runs one or more server programs, which share their resources with clients. A client usually does not share any of its resources, but it requests content or service from a server. Clients, therefore, initiate communication sessions with servers, which await incoming requests. Examples of computer applications that use the client-server model are email, network printing, and the World Wide Web.

[Wikipedia](https://en.wikipedia.org/wiki/Client%E2%80%93server_model)

## Teonet Description

Teonet is designed to create client-server systems and build networks for server applications operating within a microservice architecture. To do this, Teonet creates a network / cloud transport between its members. This transport uses UDP for communication between network peers. UDP packets are encrypted with unique keys. Teonet uses its own UDP-based protocol called Teonet Reliable UDP (TRU) for real-time communication, which allows low latency messages to be sent and protocol reliability features.

[Teonet](https://teonet.dev)

## This Sample Description

In this sample we have created three new Teonet Microservice Applications and use one existing Application.

1.  Fortune Server Application

    This is simple [Teonet](https://github.com/teonet-go/teonet) micriservice application which return linux fortune messages. The linux [fortune](https://linux.die.net/man/6/fortune) application should be installed first.

    [See Source](https://github.com/teonet-go/teofortune)

2.  Telegram Bot Client Application

    This is simple [Teonet](https://github.com/teonet-go/teonet) telegram-bot micriservice application which get fortune message from Teonet [Fortune](fortune-server-application) microservice and show it in Telegram.

    This Telegram bot: [@teofortune](https://t.me/teofortune_bot)

    [See Source](https://github.com/teonet-go/teofortune-tg)

3.  Web Site Application

    This is simple [Teonet](https://github.com/teonet-go/teonet) web-server microservice application which get fortune message from [Teonet Fortune](https://github.com/teonet-go/teofortune) microservice and show it in the site web page.

    This Website URL: http://fortune.teonet.dev

    [Teofortune-web](https://github.com/teonet-go/teofortune-web)

4.  Teonet CLI Application

    Install Teonet cli application:

        go install github.com/teonet-go/teonet ./cmd/teonet

    Run the Teonet cli application:

        teonet

    In the Teonet cli application print commands:

        connectto 8agv3IrXQk7INHy5rVlbCxMWVmOOCoQgZBF
        api 8agv3IrXQk7INHy5rVlbCxMWVmOOCoQgZBF
        api 8agv3IrXQk7INHy5rVlbCxMWVmOOCoQgZBF forta

5. Teonet GUI Application

   This is simple [Teonet](https://github.com/teonet-go/teonet) GUI apllication creating using fyne-io package. It get fortune message from [Teonet Fortune](https://github.com/teonet-go/teofortune) microservice and show it in the gui page.

   [Teofortune-gui](https://github.com/teonet-go/teofortune-gui)

6. Teonet wasm web application.

    There is [Teoproxy](https://github.com/teonet-go/teoproxy) client server packages to connect golang wasm applications with Teonet peers. It provides a websocket client server packages that can be used to connect wasm application with it own web server which runs Teonet and connects to teonet peers used in wasm application.

    [See this example Description and Source](https://github.com/teonet-go/teoproxy)

## Licence

[BSD](LICENSE)

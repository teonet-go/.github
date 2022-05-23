# Teonet v5

Teonet v5 is pure Golang implementation of Trudp and Teonet librarys and Teonet applications

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

Learn how to start building software with Teonet.

### Create simplest Teonet client application

In this application we:

- Show Teonet application logo
- Start Teonet client
- Connect to Teonet
- Connect to echo server which already run in Teonet
- Send messages to echo server and recive answers

```go
package main

import (
	"fmt"
	"time"

	"github.com/kirill-scherba/teonet"
)

const (
	appName    = "Teonet echo client sample application"
	appShort   = "teoechocli"
	appVersion = "0.0.1"
	echoServer = "dBTgSEHoZ3XXsOqjSkOTINMARqGxHaXIDxl"
	sendDelay  = 3000
)

func main() {

	// Teonet application logo
	teonet.Logo(appName, appVersion)

	// Start Teonet client
	teo, err := teonet.New(appShort)
	if err != nil {
		panic("can't init Teonet, error: " + err.Error())
	}

	// Connect to Teonet
	err = teo.Connect()
	if err != nil {
		teo.Log().Debug.Println("can't connect to Teonet, error:", err)
		// time.Sleep(1 * time.Second)
		// goto connect
		panic("can't connect to Teonet, error: " + err.Error())
	}

	// Connect to echo server
	err = teo.ConnectTo(echoServer,

		// Get messages from echo server
		func(c *teonet.Channel, p *teonet.Packet, e *teonet.Event) (proc bool) {

			// Skip not Data Events
			if e.Event != teonet.EventData {
				return
			}

			// Print received message
			fmt.Printf("got from %s, \"%s\", len: %d, id: %d, tt: %6.3fms\n\n",
				c, p.Data(), len(p.Data()), p.ID(),
				float64(c.Triptime().Microseconds())/1000.0,
			)
			proc = true

			return
		},
	)

	// Send messages to echo server
	for {
		data := []byte("Hello world!")
		fmt.Printf("send to  %s, \"%s\", len: %d\n", echoServer, data, len(data))
		_, err = teo.SendTo(echoServer, []byte("Hello world!"))
		if err != nil {
			fmt.Println(err)
		}
		time.Sleep(time.Duration(sendDelay) * time.Millisecond)
	}
}
```

### Create simplest Teonet server application

Teonet client and server application use the same functions. And there is not diferents between client and server. If aplication connect to - then it is client. If apllication wait for connections - then it is server.

So lets create our own echo server.
In this application we:

- Show Teonet application logo
- Start Teonet client, with reader to receive and process incoming messages
- Connect to Teonet
- Print application address

```go
package main

import (
	"fmt"

	"github.com/kirill-scherba/teonet"
)

const (
	appName    = "Teonet echo server sample application"
	appShort   = "teoechoserve"
	appVersion = "0.0.1"
)

func main() {

	// Teonwt application logo
	teonet.Logo(appName, appVersion)

	// Start Teonet client
	teo, err := teonet.New(appShort,
		// Main application reader - receive and process incoming messages
		func(c *teonet.Channel, p *teonet.Packet, e *teonet.Event) bool {

			// Skip not Data events
			if e.Event != teonet.EventData {
				return false
			}

			// In server mode
			if c.ServerMode() {

				// Print received message
				fmt.Printf("got from %s, \"%s\", len: %d, id: %d, tt: %6.3fms\n",
					c, p.Data(), len(p.Data()), p.ID(),
					float64(c.Triptime().Microseconds())/1000.0,
				)

				// Send answer
				answer := []byte("Teonet answer to " + string(p.Data()))
				c.Send(answer)
			}

			return true
		},
	)
	if err != nil {
		panic("can't init Teonet, error: " + err.Error())
	}

	// Connect to Teonet
	err = teo.Connect()
	if err != nil {
		panic("can't connect to Teonet, error: " + err.Error())
	}

	// Print application address
	addr := teo.Address()
	fmt.Println("Connected to teonet, this app address:", addr)

	// Wait forever
	select {}
}
```

When you start this Teonet application (`$ go run .`) it print its unical address: 

```
Connected to teonet, this app address: OUPdQ35M0x53ScObAMWiLaDv1Kn6q7KdO61
```

Use this address in your echo client created in previouse example. Replace echoServer constant value with this echo server address and start client in new terminal window.

So you have created your first Teonet client / server applications. You can run this applications in any hosts, they connect to Teonet and than client connect to server P2P without select servers or clients IP.

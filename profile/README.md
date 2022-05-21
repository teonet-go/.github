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

### Create simplest Teonet application

In this application we 
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

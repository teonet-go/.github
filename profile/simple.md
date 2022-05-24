## Simple message mode

### Teonet simple client application

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
	if err != nil {
		panic("can't connect to server, error: " + err.Error())
	}

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

### Teonet simple server application

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

	// Teonet application logo
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

Read next: [>> Command mode](command.md#command-message-mode)

Go to Home: [Table of Contents <<](README.md#table-of-Contents)

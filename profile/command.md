## Command message mode

In Teonet command mode, we send a command message. The command is the number before the message. Commands help the server to separate one type of message from another.

To send message with command we use function:

```go
SendCmdTo(cmd byte, data []byte)
```

or use Command holder:

```go
teo.Command(129, "Hello").SendTo(some_teonet_address)
```

In our command mode client example we will send command No 129 to existing Teonet sample server.

Lets create code:

### Teonet command mode client application

In this application we:

- Show Teonet application logo
- Start Teonet client
- Connect to Teonet
- Connect to echo server which already run in Teonet
- Send command to 'command mode server' and recive answers

```go
package main

import (
	"fmt"
	"time"

	"github.com/teonet-go/teonet"
)

const (
	appName       = "Teonet echo command client sample application"
	appShort      = "teoechocommandcli"
	appVersion    = "0.0.1"
	echoComServer = "WXJfYLDEtg6Rkm1OHm9I9ud9rR6qPlMH6NE"
	sendDelay     = 3000
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
		panic("can't connect to Teonet, error: " + err.Error())
	}

	// Connect to echo command server
	err = teo.ConnectTo(echoComServer,

		// Get messages from echo command server
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

	// Send messages to echo command server
	for {
		data := "Teonet developer!"
		fmt.Printf("send to  %s, \"%s\", len: %d\n", echoComServer, data,
			len(data))
		_, err = teo.Command(129, data).SendTo(echoComServer)
		if err != nil {
			fmt.Println(err)
		}
		time.Sleep(time.Duration(sendDelay) * time.Millisecond)
	}
}
```

[See this code on Github](https://github.com/teonet-go/teonet-examples/blob/main/commandcli/main.go)

This example looks like our first client, except for the Send Messages section. In this example we send message with `teo.Command(129, data).SendTo(echoComServer)`. It mean we send command 129 with data "Teonet developer!" to 'echoComServer' server.

Let's create an Echo Command Server to see the difference in a command mode server and use it to receive commands.

### Teonet command mode server application

In this application we:

- Show Teonet application logo
- Start Teonet client, with reader to receive and process incoming messages
- Connect to Teonet
- Print application address

```go
package main

import (
	"fmt"

	"github.com/teonet-go/teonet"
)

const (
	appName    = "Teonet echo command server application"
	appShort   = "teoechocommandserve"
	appVersion = "0.0.1"
)

func main() {

	// Teonet application logo
	teonet.Logo(appName, appVersion)

	// Start Teonet client
	teo, err := teonet.New(appShort,
		// Main application reader - receive and process incoming messages
		func(teo *teonet.Teonet, c *teonet.Channel, p *teonet.Packet,
			e *teonet.Event) bool {

			// Skip not Data events
			if e.Event != teonet.EventData {
				return false
			}

			// Skip not server mode messages
			if !c.ServerMode() {
				return false
			}

			// Parse command
			cmd := teo.Command(p.Data())

			// Process command
			switch cmd.Cmd {

			case 129:
				// Print received message
				fmt.Printf("got from %s, \"%s\", len: %d, id: %d, cmd: %d, "+
					"tt: %6.3fms\n",
					c, cmd.Data, len(cmd.Data), p.ID(), cmd.Cmd,
					float64(c.Triptime().Microseconds())/1000.0,
				)

				// Send answer
				answer := []byte("Teonet answer to " + string(cmd.Data))
				c.Send(answer)

			default:
				fmt.Println("got wrong command")
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

[See this code on Github](https://github.com/teonet-go/teonet-examples/blob/main/commandserve/main.go)

This sample similar to the Simple Echo Server sample. Here we change Main application reader, we add command parser: `cmd := teo.Command(p.Data())` and switch by command.

When you start this Teonet application ($ go run .) it print its unical address:

```
Connected to teonet, this app address: OUPdQ35M0x53ScObAMWiLaDv1Kn6q7KdO61
```

Use this address in your Echo Command Client created in previouse example. Replace echoComServer constant value with this Echo Command Server address and start client in new terminal window.

Read next: [>> API mode](api.md#api-message-mode)

Go to Home: [Table of Contents <<](https://github.com/teonet-go#table-of-Contents)

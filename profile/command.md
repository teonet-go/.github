## Command mode

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

	"github.com/kirill-scherba/teonet"
)

const (
	appName       = "Teonet echo command client sample application"
	appShort      = "teoechocommandcli"
	appVersion    = "0.0.1"
	echoComServer = "WXJfYLDEtg6Rkm1OHm9I9ud9rR6qPlMH6NE"
	sendDelay     = 3000
)

func main() {
	// Teonwt application logo
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

This example looks like our first client, except for the Send Messages section. In this example we send message with `teo.Command(129, data).SendTo(echoComServer)`. It mean we send command 129 with data "Teonet developer!" to 'echoComServer' server.

Let's create an Echo Command Server to see the difference in a command mode server and use it to receive commands.

### Teonet command mode server application

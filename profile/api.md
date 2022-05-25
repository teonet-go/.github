## API message mode

In Teonet api mode, we send a command message and use api interface to do it instead of regular command mode. Teonet API contains:

- command number
- command name
- command description
- usage (input parameter) description

### Teonet api mode client application

In this application we:

- Show Teonet application logo
- Start Teonet client
- Connect to Teonet
- Connect to api server which already run in Teonet
- Send api command to 'api mode server' and recive answers

```go
package main

import (
	"fmt"
	"time"

	"github.com/teonet-go/teonet"
)

const (
	appName    = "Teonet api client sample application"
	appShort   = "teoapicli"
	appVersion = "0.0.1"
	apiServer  = "WXJfYLDEtg6Rkm1OHm9I9ud9rR6qPlMH6NE"
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
		panic("can't connect to Teonet, error: " + err.Error())
	}

	// Connect to api server
	err = teo.ConnectTo(apiServer)
	if err != nil {
		panic("can't connect to server, error: " + err.Error())
	}

	// Create Teonet client API interface
	apicli, err := teo.NewAPIClient(apiServer)
	if err != nil {
		panic("can't create Api Client, error: " + err.Error())
	}

	// Send messages to api server and receive answer
	for {
		data := []byte("Developer!")
		fmt.Printf("send to  %s, \"%s\", len: %d\n", apiServer, data, len(data))
		_, err = apicli.SendTo("hello", data, func(data []byte, err error) {
			// Process error
			if err != nil {
				fmt.Println(err)
				return
			}
			// Print received message
			fmt.Printf("got from %s, \"%s\", len: %d\n\n",
				apicli.Address(), data, len(data),
			)
		})
		if err != nil {
			fmt.Println(err)
		}
		time.Sleep(time.Duration(sendDelay) * time.Millisecond)
	}
}
```

This example looks like our simple and command clients, except for the Send Messages section. In this example we send message with `apicli.SendTo("hello", data, func(data []byte, err error) { })`. It mean we send command "hello" with data "Developer!" to 'apiServer' server. Here we use text command name instead of number, and receive answer in sendTo function insted of ConnectTo as in simple and command clients.

Let's create an API Server to see the difference in a api mode server and use it to receive commands.

### Teonet api mode server application

In this application we:

- Show Teonet application logo
- Start Teonet client
- Create Teonet API
- Create API command "hello"
- Connect to Teonet
- Print application address

```go
package main

import (
	"fmt"

	"github.com/teonet-go/teonet"
)

const (
	appName    = "Teonet api server application"
	appShort   = "teoechoapiserve"
	appLong    = "This description of Teonet api server application"
	appVersion = "0.0.1"
)

func main() {

	// Teonet application logo
	teonet.Logo(appName, appVersion)

	// Start Teonet client
	teo, err := teonet.New(appShort)
	if err != nil {
		panic("can't init Teonet, error: " + err.Error())
	}

	// Create Teonet API
	api := teo.NewAPI(appName, appShort, appLong, appVersion)

	// Create API command "hello"
	cmdApi := teonet.MakeAPI2()
	cmdApi.
		SetCmd(api.Cmd(129)).                 // Command number cmd = 129
		SetName("hello").                     // Command name
		SetShort("get 'hello name' message"). // Short description
		SetUsage("<name string>").            // Usage (input parameter)
		SetReturn("<answer string>").         // Return (output parameters)
		// Command reader (execute when command received)
		SetReader(func(c *teonet.Channel, p *teonet.Packet, data []byte) bool {
			fmt.Printf("got from %s, \"%s\", len: %d, id: %d, tt: %6.3fms\n",
				c, data, len(data), p.ID(),
				float64(c.Triptime().Microseconds())/1000.0,
			)
			data = append([]byte("Hello "), data...)
			api.SendAnswer(cmdApi, c, data, p)
			return true
		}).SetAnswerMode(teonet.DataAnswer)
	api.Add(cmdApi)

	// Add API reader
	teo.AddReader(api.Reader())

	// Print API
	fmt.Printf("API description:\n\n%s\n\n", api.Help())

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

When you start this Teonet application ($ go run .) it print its unical address:

```
Connected to teonet, this app address: OUPdQ35M0x53ScObAMWiLaDv1Kn6q7KdO61
```

Use this address in your Echo Api Client created in [previouse example](#teonet-api-mode-client-application). Replace apiServer constant value with this Echo API Server address and start client in new terminal window.

Go to Home: [Table of Contents <<](https://github.com/teonet-go#table-of-Contents)

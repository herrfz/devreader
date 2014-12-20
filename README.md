Example usage: Wrap Pub-Sub ZeroMQ socket as a channel, use it in `for` and `select` construct.

```Go
package main

import (
  "encoding/hex"
  "fmt"
  "github.com/herrfz/devreader"
  zmq "github.com/pebbe/zmq4"
)

type Socket struct {
	socket *zmq.Socket
}

// Socket implements devreader interface
func (sock Socket) ReadDevice() ([]byte, error) {
	buf, err := sock.socket.Recv(0)
	return []byte(buf), err
}

func main() {
	sock, _ := zmq.NewSocket(zmq.SUB)
	defer sock.Close()
	sock.Connect("tcp://127.0.0.1:5556")
	sock.SetSubscribe("") // subscribe to all

	data_ch := devreader.MakeChannel(Socket{sock}) // Wrap sock as data_ch
	
	for {
		select {
		case buf := <-data_ch:
			fmt.Println("received:", hex.EncodeToString(buf))
		}
	}
}

```

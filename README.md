# `gethmock`

> CI RPC Mock Provider

### Standalone

Using `gethmock` as a standalone binary is easy. All you need to do is

```sh
$ go get github.com/sambacha/gethmock
$ gethmock --help
NAME:
   gethmock

USAGE:
   gethmock [global options] command [command options] [arguments...]

VERSION:
...
```
After installing it, you can start it up as a server with the `serve` command, or you can use it's `fetch` command to download sample responses to use from an ethereum  client node.

#### Examples:

#### `serve`
```sh
$ gethmock serve
```

#### `fetch`
```sh
$ gethmock fetch --base-path testdata/infura eth_chainId 
INFO[0000] starting fetch                               
INFO[0000] wrote request.json(90B), response.json(69B) to testdata/infura/eth_chainId

$ gethmock fetch --eth-client-url https://mainnet.infura.io eth_getBlockByNumber '["0xfa1b4",true]'
INFO[0000] starting fetch                               
INFO[0000] wrote request.json(126B), response.json(2.3K) to testdata/eth_getBlockByNumber/0xfa1b4/true 
```

### As a package
Using `gethmock` as a package is mostly used in your tests, so you have a reference, local, Ethereum client with preprogrammed responses you can test against.

Example initialization:
```
package test

import (
	"testing"

	"github.com/stretchr/testify/assert"
	mock "github.com/sambacha/gethmock/server"
)

func TestRequest(t *testing.T) {
	srv, err := mock.New(8545, "../testdata/mock")
	assert.Nil(t, err)
	go srv.Serve()
	defer srv.Close()

	p, err := httprpc.New("http://localhost:8545")
	assert.Nil(t, err)

	e, err := New(p)
	assert.Nil(t, err)

	n, err := e.GetBlockNumber()
	assert.Nil(t, err)

	assert.Equal(t, int64(7912466), n)
}
```
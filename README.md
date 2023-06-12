# CometMock

CometMock is a mock implementation of CometBFT.
It is meant to be used as a drop-in replacement for CometBFT in end-to-end tests.
Some of the reasons to use CometMock instead of CometBFT are:
* More reliable and faster block times: CometBFT runs a consensus algorithm, which involves many network communications, and non-determinism in the network layer can lead to varying block times that can make tests flaky.
CometMock instead directly communicates with applications via ABCI, mimicking the behaviour of many CometBFT instances coming to consensus, but with much fewer network communications.
* More control: When transactions are broadcasted, CometMock immediately includes them in the next block. Planned features also include simulating downtime without the need to bother with the network or killing processes. Additionally, CometMock will allow fast-forwarding time,
letting arbitrary time pass to the view of the application, without needing to actually wait.

On a technical level, CometMock communicates with applications via ABCI through GRPC calls. It calls BeginBlock, DeliverTx, EndBlock and Commit like CometBFT does during normal execution.

Currently, CometMock imitates CometBFT v0.34. It offers *many* of the RPC endpoints offered by Comet (https://docs.cometbft.com/v0.34/rpc/),
in particular it supports the subset used by Gorelayer (https://github.com/cosmos/relayer/).
See the endpoints offered here: https://github.com/p-offtermatt/CometMock/blob/ee5a1e150a92c4cedf8e9284c82489307730ac2f/cometmock/rpc_server/routes.go#L30C2-L53

## Installation

Run `go install ./cometmock`, then you can run `cometmock` to see usage information.
CometMock was tested with `go version go1.20.3 darwin/arm64`.

## How to use

To run CometMock, start your (cosmos-sdk) application instances with the flags ```--with-tendermint=false, --transport=grpc```.
After the applications started, start CometMock like this
```
cometmock {app_address1,app_address2,...} {genesis_file} {cometmock_listen_address} {home_folder1,home_folder2,...}
```

where 
* the `app_addresses` are the `--address` flags of the applications (this is by default `"tcp://0.0.0.0:26658"`
* the `genesis_file` is the genesis json that is also used by apps,
* the `cometmock_listen_address` can be freely chosen and will be the address that requests that would normally go to CometBFT rpc endpoints need to be directed to
* the `home_folders` are the home folders of the applications, in the same order as the `app_addresses`. This is required to use the private keys in the application folders to sign as appropriate validators.

When calling the cosmos sdk cli, use as node address the `cometmock_listen_address`,
e.g. `simd q bank total --node {cometmock_listen_address}`.

## Disclaimer and License Information

Copyright © 2023 Informal Systems Inc. and CometMock authors.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use the files in this repository except in compliance with the License. You may obtain a copy of the License at

```https://www.apache.org/licenses/LICENSE-2.0```

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.

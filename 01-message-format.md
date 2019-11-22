# BOLD #1: Message Format

## Overview 

All messages sent between nodes have headers. The `SessionInit` and `SessionAck` initial handshake messages are sent unencrypted, and all other messages are encrypted and authenticated using the keys that are generated during the initial handshake.

All messages payloads are serialized using **Protocol Buffers**. 

Protocol buffers are a language-neutral, platform-neutral, extensible mechanism for serializing structured data. You can find [Protobuf's documentation on the Google Developers site](https://developers.google.com/protocol-buffers/).

To develop with protocol buffers you will need to install the protocol buffer compiler (to compile `.proto` files) and the protocol buffer runtime for your chosen programming language.

Currently OpenDEX supports Java, Python, Go, Rust, C, C++, Objective-C, C#, Dart, Ruby, Perl, Haskell, Javascript, [and more](https://github.com/protocolbuffers/protobuf/blob/master/docs/third_party.md#programming-languages).

### The unencrypted message

| Size (bytes) | Name     | Data Type              | Description                                                                                |
|--------------|----------|------------------------|--------------------------------------------------------------------------------------------|
| 4            | magic    | uint32                 | Magic value to specify the message's network                                               |
| 4            | length   | uint32                 | Payload length                                                                             |
| 4            | type     | uint32                 | Message type                                                                               |
| 4            | checksum | uint32                 | First 4 bytes of sha256 hash of JSON-serialized message                                    |
| length       | payload  | bytes                  | The actual data                                                                            |
 
#### Magic values

| Network | Magic value    |
|---------|----------------|
| mainnet | `0xd9b4bef9`   |
| testnet | `0x0709110b`   |
| simnet  | `0x12141c16`   |
| regnet  | `0xdab5bffa`   |

### The encrypted message

| Size (bytes) | Name       | Data Type              | Description                                                                                |
|--------------|------------|------------------------|--------------------------------------------------------------------------------------------|
| 4            | length     | uint32                 | Length of the payload                                                                      |
| length       | ciphertext | bytes                  | The encrypted data
          
#### Ciphertext pre-encryption format
                                                           
| Size (bytes) | Name       | Data Type              | Description                                                                                |
|--------------|------------|------------------------|--------------------------------------------------------------------------------------------|
| 4            | length     | uint32                 | Payload length                                                                             |
| 4            | type       | uint32                 | Message type                                                                               |
| length       | payload    | bytes                  | The actual data                                                                            |

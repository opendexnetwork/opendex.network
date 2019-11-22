# BOLD #2: Wire Protocol

All messages payload is serialized using **Protocol Buffers (protobuf)**. 

*Protobuf* is language-neutral, platform-neutral, extensible mechanism for serializing structured data. You can find [protobuf's documentation on the Google Developers site](https://developers.google.com/protocol-buffers/).

To install *Protobuf*, one needs to install the protocol compiler (used to compile `.proto` files) and the protobuf runtime for the chosen programming language.


###Framing

All messages including initial handshake are framed. A frame carries a single message. Framed messages yield reasonable demarcation points for message authentication codes, and so supporting an encrypted and authenticated stream becomes straight-forward. Frames are encrypted and authenticated via key material generated during the handshake.

#### The unencrypted frame

| Fixed Size| Name     | Data Type              | Description                                                                                |
------------|----------|------------------------|--------------------------------------------------------------------------------------------|
| 4         | magic    | uint32, little-endian  | Magic value indicating message origin network                                              |
| 4         | length   | uint32, little-endian  | Length of payload in bytes                                                                 |
| 4         | type     | uint32, little-endian  | The type of the carried message                                                            |
| 4         | checksum | uint32, little-endian  | The message JSON-serialized sha256 output first 4 bytes, to be used for integrity checks   |
| ?         | payload  | bytes                  | The actual data                                                                            |
 

##### Origin network values

| Network | Magic value    |
|---------|----------------|
| mainnet | `0xd9b4bef9`   |
| testnet | `0x0709110b`   |
| simnet  | `0x12141c16`   |
| regnet  | `0xdab5bffa`   |

### The encrypted frame

| Fixed Size| Name       | Data Type              | Description                                                                                |
------------|------------|------------------------|--------------------------------------------------------------------------------------------|
| 4         | length     | uint32, little-endian  | Length of payload in bytes                                                                 |
| ?         | ciphertext | bytes                  | The encrypted data
         
##### The ciphertext frame (pre-encryption)
                                                           
| Fixed Size| Name       | Data Type              | Description                                                                                |
------------|------------|------------------------|--------------------------------------------------------------------------------------------|
| 4         | length     | uint32, little-endian  | Length of payload in bytes                                                                 |
| 4         | type       | uint32, little-endian  | The type of the carried message                                                            |
| ?         | payload    | bytes                  | The actual data                                                                            |




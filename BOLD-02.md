# BOLD-02

## Wire Protocol

All messages payload is serialized using **Protocol Buffers** (**protobuf**). 

Protobuf is language-neutral, platform-neutral, extensible mechanism for serializing structured data. You can find [protobuf's documentation on the Google Developers site](https://developers.google.com/protocol-buffers/).

To install protobuf, one needs to install the protocol compiler (used to compile .proto files) and the protobuf runtime for the chosen programming language.

...

**<span style="text-decoration:underline;">Framing</span>**

All messages including initial handshake are framed. A frame carries a single message. Framed messages yield reasonable demarcation points for message authentication codes, and so supporting an encrypted and authenticated stream becomes straight-forward. Frames are encrypted and authenticated via key material generated during the handshake.

The frame header provides information about the message origin network, length and type. In addition, it proves checksum for integrity check. 

...

| network magic value 0 | length 4 | type 8 | checksum 12 | payload 16 | 

...

**<span style="text-decoration:underline;">Origin network values</span>**


<table>
  <tr>
   <td><strong>Network </strong>
   </td>
   <td><strong>Magic value</strong>
   </td>
  </tr>
  <tr>
   <td>mainnet
   </td>
   <td>0xd9b4bef9
   </td>
  </tr>
  <tr>
   <td>testnet
   </td>
   <td>0x0709110b
   </td>
  </tr>
  <tr>
   <td>simnet
   </td>
   <td>0x12141c16
   </td>
  </tr>
  <tr>
   <td>regnet
   </td>
   <td>0xdab5bffa
   </td>
  </tr>
</table>


...


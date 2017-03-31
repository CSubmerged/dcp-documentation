### Close Stream (opcode 0x52)

Sent to server controling an DCP stream to close the stream for a named vbucket as soon as possible.

The request:
* Must not extras
* Must not have key
* Must not have value

The layout of a message looks like:

      Byte/     0       |       1       |       2       |       3       |
         /              |               |               |               |
        |0 1 2 3 4 5 6 7|0 1 2 3 4 5 6 7|0 1 2 3 4 5 6 7|0 1 2 3 4 5 6 7|
        +---------------+---------------+---------------+---------------+
       0| 0x80          | 0x52          | 0x00          | 0x00          |
        +---------------+---------------+---------------+---------------+
       4| 0x00          | 0x00          | 0x00          | 0x05          |
        +---------------+---------------+---------------+---------------+
       8| 0x00          | 0x00          | 0x00          | 0x00          |
        +---------------+---------------+---------------+---------------+
      12| 0xde          | 0xad          | 0xbe          | 0xef          |
        +---------------+---------------+---------------+---------------+
      16| 0x00          | 0x00          | 0x00          | 0x00          |
        +---------------+---------------+---------------+---------------+
      20| 0x00          | 0x00          | 0x00          | 0x00          |
        +---------------+---------------+---------------+---------------+
    DCP_CLOSE_STREAM command
    Field        (offset) (value)
    Magic        (0)    : 0x80
    Opcode       (1)    : 0x52
    Key length   (2,3)  : 0x0000
    Extra length (4)    : 0x00
    Data type    (5)    : 0x00
    Vbucket      (6,7)  : 0x0005
    Total body   (8-11) : 0x00000000
    Opaque       (12-15): 0xdeadbeef
    CAS          (16-23): 0x0000000000000000

If received on the consumer side the consumer will close the stream for the specified vbucket imediately and let the producer know that the stream is closed as soon as it receives a message bound for that vbucket.

If received on the producer side the producer will send an end stream message to the consumer indicating that the stream was closed by force. The producer may still receive response messages from the consumer for this stream.

### Returns

A status code indicating whether or not the operation was successful.

### Errors

**PROTOCOL_BINARY_RESPONSE_KEY_ENOENT (0x01)**

If a stream does not exist for the vbucket specfied on this connection.

**PROTOCOL_BINARY_RESPONSE_EINVAL (0x04)**

If data in this packet is malformed or incomplete then this error is returned.

**(Disconnect)**

If the connection state no longer exists on the server. The most likely reason this will happen is if another connection is made to the server from a different client with the same name as the current connection.

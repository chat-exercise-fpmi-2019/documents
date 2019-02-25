# Chat Prototol

This document describes network protocol for "client-server chat" educational exercise.

The chat consist of server and client. Client can issue three types of commands:

* Set username
* Send private message
* Send broadcast message

Server can send three types of responses to client.

* Incoming message
* Failure reply for a client command
* Custom notification

This document is based text provided by one of students.

## Connection

Clients establish a TCP (see RFC 793) connection with a server and communicate with text messages.

All text MUST be encoded as UTF-8 (RFC 3629) for network transmission.

Server MAY use any name for clients that have not specified one.

Clients and servers MAY do anything, including dropping the connection, after recieving a message badly formed according to this RFC.

Recommended port for server is 50555.


## Messages send from client to server

### USERNAME

The USERNAME message is being sent by clients and MUST be of form:

* string `USERNAME` in upper case,
* followed by exactly one normal space character (ASCII "SP"),
* followed by the username represented by a string of upper or lower case letters, numbers or underscores,
* followed by exactly one normal newline character (ASCII "LF").

Username length MUST be between 1 and 64 characters.

Example, as a C string literal: "USERNAME xxxNogebator69xxx\n"

Servers SHOULD use the usernames provided by clients this way when retransmitting their messages to other clients.

If a user with the specified username already exists, servers MUST accept the message as well-formed and SHOULD reply to the sender with an appropriate INFO message, while ignoring the new username.

### SEND

The SEND message is being sent by clients and MUST be of form:

* string `SEND` in upper case;
* followed by exactly one normal space character;
* followed by the recipient username represented by a string of upper or lower case letters, numbers or underscores;
* followed by exactly one normal space character;
* followed by the message body length (LENGTH) in bytes, a nonnegative integer represented as a string of decimal digits without leading zeros;
* followed by exactly one normal newline character;
* followed by the message body represented as an exactly LENGTH bytes long sequence of arbitrary symbols;
* followed by exactly one normal newline character.

Example, as a C string literal: "SEND user123 17\nhi!\nhow are you?\n\n"

Servers SHOULD forward the message to the specified recipient using the `MESSAGE` command.

Servers MUST accept zero-body-length messages as well-formed.
Servers MUST accept messages to recipients that cannot be found as well-formed and in this case SHOULD reply to the sender with an appropriate INFO message.

### BROADCAST

The BROADCAST message being is sent by clients and MUST be of form:

* string `BROADCAST` in upper case;
* followed by exactly one normal space character;
* followed by the message body length (LENGTH) in bytes, a nonnegative integer represented as a string of decimal digits without leading zeros;
* followed by exactly one normal newline character;
* followed by the message body represented as an exactly LENGTH bytes long sequence of arbitrary symbols;
* followed by exactly one normal newline character.

Example, as a C string literal: "BROADCAST 17\nhi!\nhow are you?\n\n"

Servers SHOULD forward the message to all other users using the `MESSAGE` command.

Servers MUST accept zero-body-length messages as well-formed.


## Messages from server to client

### MESSAGE

The MESSAGE message is sent by servers and MUST be of form:

* string `MESSAGE` in upper case;
* followed by exactly one normal space character;
* followed by the sender username represented by a string of upper or lower case letters, numbers or underscores;
* followed by exactly one normal space character;
* followed by the message body length (LENGTH) in bytes, a nonnegative integer represented as a string of decimal digits without leading zeros;
* followed by exactly one normal newline character;
* followed by the message body represented as an exactly LENGTH bytes long sequence of arbitrary symbols;
* followed by exactly one normal newline character.

Example, as a C string literal: "MESSAGE user122 17\nhi!\nhow are you?\n\n"

### INFO

The INFO message is sent by servers and MUST be of form:

* string `INFO` in upper case;
* followed by exactly one normal space character;
* followed by the message body length (LENGTH) in bytes, a nonnegative integer represented as a string of decimal digits without leading zeros;
* followed by exactly one normal newline character;
* followed by the message body represented as an exactly LENGTH bytes long sequence of arbitrary symbols;
* followed by exactly one normal newline character.

Example, as a C string literal: "INFO 28\nuser johnny entered the chat\n"

Servers SHOULD send INFO messages to describe connects and disconnects of users, errors with delivery of previous messages, and other service information.

## Example session

Here is example session of client connecting to server and

* Setting username
* Sending broadcast message
* Sending private message to nonexisting user
* Sending message to a valid user
* Receiving private message
* Receiving custom server notification

```
To server, 13 bytes
 55 53 45 52 4e 41 4d 45 20 6c 6f 6c 0a           USERNAME lol.

To server, 19 bytes
 42 52 4f 41 44 43 41 53 54 20 36 0a              BROADCAST 6.
 48 65 6c 6c 6f 21 0a                             Hello!.

To server, 18 bytes
 53 45 4e 44 20 6e 6f 62 6f 64 79 20 33 0a        SEND nobody 3.
 2e 2e 2e 0a                                      ....

From server, 27 bytes
 49 4e 46 4f 20 31 38 0a                          INFO 18.
 55 73 65 72 6e 61 6d 65 20 6e 6f 74 20 66 6f 75  Username not fou
 6e 64 0a                                         nd.

To server, 16 bytes
 53 45 4e 44 20 71 71 71 20 34 0a                 SEND qqq 4.
 31 32 33 34 0a                                   1234.

From server, 19 bytes
 4d 45 53 53 41 47 45 20 71 71 71 20 34 0a        MESSAGE qqq 4.
 35 36 37 38 0a                                   5678.

From server, 26 bytes
 49 4e 46 4f 20 31 37 0a                          INFO 17.
 55 73 65 72 20 71 71 71 20 71 75 69 74 74 69 6e  User qqq quittin
 67 0a                                            g.
```

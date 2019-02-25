# documents
Repository for chat protocol descriptions (RFCs)

Each student is to publish here a document describing chat protocol.
Implementors of clients and servers according to those protocols are to comment.

There is [a sample document](sample.md) available. Note that I'll check protocols for uniqueness.

The earlier you are to submit, the more choice in used ideas you have.

## Design options

### Underlying protocol

* TCP
* UDP
* Custom

### Usage of existing protocols

* No usage, simple protocol
* HTTP-based
* IRC-based
* XMPP-based

### Message delimiting for simple protocols

* No delimiting, single message only or UDP
* Fixed size messages
* Delimiting bytes or byte sequences
* Length-prefixed.

### Valid username options

* Numbers
* Words (strings without spaces)
* Single letters
* Arbitrary text strings

### Command replies

* No replies
* Error codes (200 OK, 400 Failed, etc.)
* Free-form text strings
* Messages sent by pseudo-user

Command replies may be for all commands or only for failed commands

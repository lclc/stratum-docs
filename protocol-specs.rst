Electrum protocol specification
===============================

Stratum is a universal bitcoin communication protocol used
mainly by bitcoin client Electrum and miners.


Format
------

Stratum protocol is based on `JSON-RPC 2.0`_ (although it doesn't
include "jsonrpc" information in every message). Each
message has to end with a line end character (\\n).

.. _JSON-RPC 2.0: http://www.jsonrpc.org/specification

Request
```````

Typical request looks like this:

.. code-block:: json

   { "id": 0, "method":"some.stratum.method", "params": [] }

- id begins at 0 and every message has its unique id number
- list and description of possible methods is below
- params is an array, e.g.: [ "1myfirstaddress", "1mysecondaddress", "1andonemoreaddress" ]
- params has to be sent, even when there are no parameters

Response
````````
Responses are similar:

.. code-block:: json

   { "id": 0, "result": "616be06545e5dd7daec52338858b6674d29ee6234ff1d50120f060f79630543c"}

- id is copied from the request message (this way client can pair each
  response to one of his requests)
- result can be:

  - null
  - a string (as shown above)
  - a hash, e.g.:

    .. code-block:: json

       { "nonce": 1122273605, "timestamp": 1407651121, "version": 2, "bits": 406305378 }

  - an array of hashes, e.g.:

    .. code-block:: json

       [ { "tx_hash":
       "b87bc42725143f37558a0b41a664786d9e991ba89d43a53844ed7b3752545d4f",
       "height": 314847 }, { "tx_hash":
       "616be06545e5dd7daec52338858b6674d29ee6234ff1d50120f060f79630543c",
       "height": 314853 } ]

Methods
-------

server.version
``````````````

This is usually the first client's message, plus **it's sent every
minute as a keep-alive message**. Client sends its own version and
version of the protocol it supports. Server responds with its
supported version of the protocol (higher number at server-side is
usually compatible).

*request:*

.. code-block:: json

   { "id": 0, "method": "server.version", "params": [ "1.9.5", "0.6" ] }

*response:*

.. code-block:: json

   { "id": 0, "result": "0.8" }

server.banner
`````````````
*request:*

.. code-block:: json

   { "id": 1, "method": "server.banner", "params": [] }

*response:*

.. code-block:: json

   {"id": 1, "result": "This node runs bitcoind 0.11.1 (can be any text)"}

server.donation_address
```````````````````````
*request:*

.. code-block:: json

   { "id": 1, "method": "server.donation_address", "params": [] }

*response:*

.. code-block:: json

   {"id":1,"result":"12o9kVUrMLTivkk74HnNE76bTGmctit4ZV"}

server.peers.subscribe
``````````````````````

Client can this way ask for a list of other active
servers. Servers are connected to an IRC channel (#electrum
at freenode.net) where they can see each other. Each server
announces its version, history pruning limit of every
address ("p100", "p10000" etc.–the number means how many
transactions the server may keep for every single address)
and supported protocols ("t" = tcp@50001, "h" = http@8081,
"s" = tcp/tls@50002, "g" = https@8082; non-standard port
would be announced this way: "t3300" for tcp on port 3300).


**Note:** At the time of writing there isn't a true
subscription implementation of this method, but servers
only send one-time response. They don't send notifications
yet.

*request:*

.. code-block:: json

   { "id": 3, "method":
   "server.peers.subscribe", "params": [] }

*response:*

.. code-block:: json

   { "id": 3, "result": [ [ "83.212.111.114",
   "electrum.stepkrav.pw", [ "v0.9", "p100", "t", "h", "s",
   "g" ] ], [ "23.94.27.149", "ultra-feather.net", [ "v0.9",
   "p10000", "t", "h", "s", "g" ] ], [ "88.198.241.196",
   "electrum.be", [ "v0.9", "p10000", "t", "h", "s", "g" ] ] ]
   }

blockchain.numblocks.subscribe
``````````````````````````````
A request to send to the client notifications about new
blocks height. Responds with the current block height.

*request:*

.. code-block:: json

   { "id": 5, "method":
   "blockchain.numblocks.subscribe", "params": [] }


*response:*

.. code-block:: json

   { "id": 5, "result": 316024 }

*message:*

.. code-block:: json

   { "id": null, "method":
   "blockchain.numblocks.subscribe", "params": 316024 }

blockchain.headers.subscribe
````````````````````````````

A request to send to the client notifications about new
blocks in form of parsed blockheaders.

*request:*

.. code-block:: json

   { "id": 5, "method":
   "blockchain.headers.subscribe", "params": [] }

*response:*

.. code-block:: json

   { "id": 5, "result": { "nonce":
   3355909169, "prev_block_hash":
   "00000000000000002b3ef284c2c754ab6e6abc40a0e31a974f966d8a2b4d5206",
   "timestamp": 1408252887, "merkle_root":
   "6d979a3d8d0f8757ed96adcd4781b9707cc192824e398679833abcb2afdf8d73",
   "block_height": 316023, "utxo_root":
   "4220a1a3ed99d2621c397c742e81c95be054c81078d7eeb34736e2cdd7506a03",
   "version": 2, "bits": 406305378 } }

*message:*

.. code-block:: json

   { "id": null, "method":
   "blockchain.headers.subscribe", "params": [ { "nonce":
   881881510, "prev_block_hash":
   "00000000000000001ba892b1717690900ae476857120a78fb50825f8b67a42d4",
   "timestamp": 1408255430, "merkle_root":
   "8e92bdbf1c5c581b5942fc290c6c52c586f091b279ea79d4e21460e138023839",
   "block_height": 316024, "utxo_root":
   "060f780c0dd07c4289aaaa2ef24723f73380095b31d60795e1308170ec742ffb",
   "version": 2, "bits": 406305378 } ] }

blockchain.address.subscribe
````````````````````````````

A request to send to the client notifications when status
(i.e., transaction history) of the given address changes.
Status is a hash of the transaction history. If there isn't
any transaction for the address yet, the status is null.

*request:*

.. code-block:: json

   { "id": 6, "method":"blockchain.address.subscribe", "params": ["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }

*response:*

.. code-block:: json

   { "id": 6, "result":"b87bc42725143f37558a0b41a664786d9e991ba89d43a53844ed7b3752545d4f" }

*message:*

.. code-block:: json

   { "id": null, "method":"blockchain.address.subscribe", "params": ["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L","690ce08a148447f482eb3a74d714f30a6d4fe06a918a0893d823fd4aca4df580"]}

blockchain.address.get_history
``````````````````````````````

*request:*

.. code-block:: json

   {"id": 1, "method":
   "blockchain.address.get_history", "params":
   ["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }

*response:*

.. code-block:: json

   {"id": 1, "result": [{"tx_hash":
   "ac9cd2f02ac3423b022e86708b66aa456a7c863b9730f7ce5bc24066031fdced",
   "height": 340235}, {"tx_hash":
   "c4a86b1324f0a1217c80829e9209900bc1862beb23e618f1be4404145baa5ef3",
   "height": 340237}]}


blockchain.address.get_mempool
``````````````````````````````
Get all transactions that are currently in the mempool for a given address.
get_mempool is not used by the Electrum client and is only implemented in the Python server.

Example CL call: (echo '{ "id": 1, "method":"blockchain.address.get_mempool", "params":["13pqgvvX3mk3vUP3JNAXFbptF8AFvi69Mr"] }'; sleep 2) | ncat --ssl ecdsa.net 50002


*request:*

.. code-block:: json

   { "id": 1, "method":"blockchain.address.get_mempool", "params":["13pqgvvX3mk3vUP3JNAXFbptF8AFvi69Mr"] }

*response (when transaction not yet confirmed):*

.. code-block:: json

   {"id": 1, "result": [{"tx_hash": "26c5af540ffade3ef4b9d9e446decf6131329492c4c980ba2c24c0e00e75539d", "height": 0}]}


*response (with transaction confirmed):*

.. code-block:: json
   
   {"id": 1, "result": []}


blockchain.address.get_balance
``````````````````````````````

*request:*

.. code-block:: json

   { "id": 1, "method":"blockchain.address.get_balance", "params":["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }

*response:*

.. code-block:: json

   {"id": 1, "result": {"confirmed": 533506535, "unconfirmed": 27060000}}


blockchain.address.get_proof
````````````````````````````

blockchain.address.listunspent
``````````````````````````````

*request:*

.. code-block:: json

   { "id": 1, "method":
   "blockchain.address.listunspent", "params":
   ["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }<br/>

*response:*

.. code-block:: json

   {"id": 1, "result": [{"tx_hash":
   "561534ec392fa8eebf5779b233232f7f7df5fd5179c3c640d84378ee6274686b",
   "tx_pos": 0, "value": 24990000, "height": 340242},
   {"tx_hash":"620238ab90af02713f3aef314f68c1d695bbc2e9652b38c31c025d58ec3ba968",
   "tx_pos": 1, "value": 19890000, "height": 340242}]}

blockchain.utxo.get_address
```````````````````````````
Returns the address of the unspent output at position X for transaction Y.
get_address is not used by the Electrum client and is only implemented in the Python server.

Example CL call: (echo '{ "id": 1, "method":"blockchain.utxo.get_address", "params":["385c887f5566a3c09138d858c81ab8cb1a6d14f57ecf1e6b1af6ef4d3958c449", 0] }'; sleep 2) | ncat ecdsa.net 50001

*request (Position 0 being an unspent output):*

.. code-block:: json

   { "id": 1, "method":"blockchain.utxo.get_address", "params":["385c887f5566a3c09138d858c81ab8cb1a6d14f57ecf1e6b1af6ef4d3958c449", 0] }

*response:*

.. code-block:: json

   {"id": 1, "result": "1KdKnJ3EawVUVzPjpiU9w8ui6GLdUofMRu"}

*request (Position 1 being a spent output):*

.. code-block:: json

   { "id": 1, "method":"blockchain.utxo.get_address", "params":["385c887f5566a3c09138d858c81ab8cb1a6d14f57ecf1e6b1af6ef4d3958c449", 1] }

*response:*

.. code-block:: json

   {"id": 1, "result": null}


blockchain.block.get_header
```````````````````````````
Returns the block at height X.

Example CL call: (echo '{"id": 1, "method": "blockchain.block.get_header", "params": [1] }'; sleep 2) | ncat ecdsa.net 50001

*request:*

.. code-block:: json

   {"id": 1, "method": "blockchain.block.get_header", "params": [1] }

*response:*

.. code-block:: json

   {"id": 1, "result": {"nonce": 2573394689, "prev_block_hash": "000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f", "timestamp": 1231469665, "merkle_root": "0e3e2357e806b6cdb1f70b54c3a3a17b6714ee1f0e68bebb44a74b1efd512098", "block_height": 1, "version": 1, "bits": 486604799}}


blockchain.block.get_chunk
``````````````````````````
Returns the address of the unspent output at position X for transaction Y.
get_chunk is not used by the Electrum client and is only implemented in the Python server.

Example CL call: (echo '{ "id": 1, "method":"blockchain.block.get_chunk", "params":["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }'; sleep 2) | ncat ecdsa.net 50001

*request:*

.. code-block:: json

   { "id": 1, "method":"blockchain.block.get_chunk", "params":["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }

*response:*

.. code-block:: json

   {"id": 1, "result": "010000006397bb6abd4fc521c0d3f6071b5650389f0b4551bc40b4e6b067306900000000ace470aecda9c8818c8fe57688cd2a772b5a57954a00df0420a7dd546b6d2c576b0e7f49ffff001d33f0192f010...rest of the block as raw hex"}
   

blockchain.transaction.broadcast
````````````````````````````````

Submits raw transaction (serialized, hex-encoded) to the network. Returns transaction id, or an error if the transaction is invalid for any reason.

*request:*

.. code-block:: json

   { "id": 1, "method":
   "blockchain.transaction.broadcast", "params":
   "0100000002f327e86da3e66bd20e1129b1fb36d07056f0b9a117199e759396526b8f3a20780000000000fffffffff0ede03d75050f20801d50358829ae02c058e8677d2cc74df51f738285013c260000000000ffffffff02f028d6dc010000001976a914ffb035781c3c69e076d48b60c3d38592e7ce06a788ac00ca9a3b000000001976a914fa5139067622fd7e1e722a05c17c2bb7d5fd6df088ac00000000" }<br/>

*response:*

.. code-block:: json

   {"id": 1, "result": "561534ec392fa8eebf5779b233232f7f7df5fd5179c3c640d84378ee6274686b"}

blockchain.transaction.get_merkle
`````````````````````````````````
Returns all transactions needed to build the merkle root to proof transaction X is in block with height Y.

Example CL call: (echo '{ "id": 0, "method": "blockchain.transaction.get_merkle", "params": [ "6cb18f11668ea7bdad19ee7db975c50c3f72e44c3858faecea1ee10c6826d4c9", 394997 ] }'; sleep 2) | ncat ecdsa.net 50001

*request:*

.. code-block:: json

   { "id": 0, "method": "blockchain.transaction.get_merkle", "params": [ "6cb18f11668ea7bdad19ee7db975c50c3f72e44c3858faecea1ee10c6826d4c9", 394997 ] }

*response:*

.. code-block:: json

{"id": 0, "result": {"merkle": ["c0c280480c91ff31752b6b6969fc024057de30d5fc2ffe9ad56316a06ead5c01", "29e9b3900c48cf7ac82523c88dad5501b6ea4cf7062aaa315c7ffac9066cf9ec", "508c77c8a5ed6045aba1708d51fcad8ad49aaa3e96abf2239d172f60cf67663d", "ca1b0f51b12d092c7a2f18210b3a2cd5abaede495777b8e6f0f96148f708bc4c", "d5be82b205a3b49459ec2cfb10c543e79de0b05888a573601197b8fc893322a6", "985f56031fbcf0642ccd6f2c16ddb3d37a88bf188f91920510b0e905f4815b33", "0282ceaa6c29a7b8353cf8d208c4a47a58a961e7a44700d5bbfe092b54467ea9", "a45aef601f3e16139bef5084bc739f52d3175821c1e50259f1451fe37e3cc351", "06e221acda11b7c899dd0fa0b7a9ceaf631c4b284d106a0ddc27848271254cbc", "460835251b1c156c99f5847fac63eae52946f881803cde06238aadcf5e499ddd", "768bfaa02ba785bc8fffda48f8942d974fa7e31eb8e9d9f7a1fbb8e3d9b419c7", "1c4507d30da875b6645d2f26e5a423346fb1d6e4e69d6683061d872b97e80285"], "pos": 5, "block_height": 394997}}


blockchain.transaction.get
``````````````````````````

Method for obtaining raw transaction (hex-encoded) for
given txid. If the transaction doesn't exist, an error is
returned.

*request:*

.. code-block:: json

   { "id": 17, "method":"blockchain.transaction.get", "params": [
   "0e3e2357e806b6cdb1f70b54c3a3a17b6714ee1f0e68bebb44a74b1efd512098"
   ] }

*response:*

.. code-block:: json

   { "id": 17, "result":"01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff0704ffff001d0104ffffffff0100f2052a0100000043410496b538e853519c726a2c91e61ec11600ae1390813a627c66fb8be7947be63c52da7589379515d4e0a604f8141781e62294721166bf621e73a82cbf2342c858eeac00000000"}

*error:*

.. code-block:: json

   { "id": 17, "error": "{ u'message': u'No information available about transaction', u'code': -5 }" }


blockchain.estimatefee
``````````````````````

Estimates the transaction fee per kilobyte that needs to be paid for a transaction to be included within a certain number of blocks.
Parameter: How many blocks the transaction may wait before being included

If the node doesn’t have enough information to make an estimate, the value -1 will be returned

*request:*

.. code-block:: json

   { "id": 17, "method":"blockchain.estimatefee", "params": 6 }

*response:*

.. code-block:: json

   { "id": 17, "result": 0.00026809}


blockchain.relayfee
``````````````````````

Relay fee per kilobyte that needs to be paid for a transaction to be relayed by the node.
Not implemented in the (original) Python Electrum Server.

Example CL call: (echo '{ "id": 5, "method": "blockchain.relayfee", "params": [] }'; sleep 2) | ncat ecdsa.net 50001

*request:*

.. code-block:: json

   { "id": 5, "method": "blockchain.relayfee", "params": [] }

*response:*

.. code-block:: json

   {"id": 5, "result": 5e-05}


External links
--------------

- https://docs.google.com/a/palatinus.cz/document/d/17zHy1SUlhgtCMbypO8cHgpWH73V5iUQKk_0rWvMqSNs/edit?hl=en_US" original Slush's specification of Stratum protocol
- http://mining.bitcoin.cz/stratum-mining specification of Stratum mining extension

Manual testing using netcat
-----------------------

A server running the Electrum-Server (Python implementation) on mainnet: *ecdsa.net 50001*

A server running the jelectrum (Java implementation) on mainnet: *b.1209k.com 50001*



**Server methods**

> (echo '{ "id": 0, "method": "**server.version**", "params": [ "2.7.11", "1.0" ] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 0, "method": "**server.version**", "params": [ "2.7.11", "1.0" ] }'; sleep 1) | ncat --**ssl** localhost 50002

> (echo '{ "id": 0, "method": "**server.banner**", "params": [] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 0, "method": "**server.banner**", "params": [] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{ "id": 0, "method": "**server.donation_address**", "params": [] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 0, "method": "**server.donation_address**", "params": [] }'; sleep 0.1) | ncat --**ssl** localhost 50002

**Blockchain methods**

> (echo '{ "id": 0, "method": "**blockchain.transaction.get_merkle**", "params": [ "6cb18f11668ea7bdad19ee7db975c50c3f72e44c3858faecea1ee10c6826d4c9", 394997 ] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 0, "method": "**blockchain.transaction.get_merkle**", "params": [ "6cb18f11668ea7bdad19ee7db975c50c3f72e44c3858faecea1ee10c6826d4c9", 394997 ] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{ "id": 17, "method":"**blockchain.transaction.get**", "params": ["503f4d8a8665122e750e83b80b37fb69fe4ce090e80adc2998dd033c2028cc0e"] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 17, "method":"**blockchain.transaction.get**", "params": ["503f4d8a8665122e750e83b80b37fb69fe4ce090e80adc2998dd033c2028cc0e"] }'; sleep 0.1) | ncat --**ssl** localhost 50002


> (echo '{"id": 1, "method": "**blockchain.block.get_header**", "params": [1] }'; sleep 0.1) | ncat localhost 50001

> (echo '{"id": 1, "method": "**blockchain.block.get_header**", "params": [1] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{ "id": 5, "method": "**blockchain.relayfee**", "params": [] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 5, "method": "**blockchain.relayfee**", "params": [] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{ "id": 5, "method": "**blockchain.estimatefee**", "params": [10] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 5, "method": "**blockchain.estimatefee**", "params": [10] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{ "id": 5, "method": "**blockchain.transaction.broadcast**", "params": ["rawtx"] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 5, "method": "**blockchain.transaction.broadcast**", "params": ["rawtx"] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{"id": 1, "method": "**blockchain.address.get_history**", "params": ["mv6vQZ4eWuL9uoGEbNxtCZmZuqdtLtrghW"] }'; sleep 0.1) | ncat localhost 50001

> (echo '{"id": 1, "method": "**blockchain.address.get_history**", "params": ["mv6vQZ4eWuL9uoGEbNxtCZmZuqdtLtrghW"] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{ "id": 1, "method": "**blockchain.address.listunspent**", "params": ["mv6vQZ4eWuL9uoGEbNxtCZmZuqdtLtrghW"] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 1, "method": "**blockchain.address.listunspent**", "params": ["mv6vQZ4eWuL9uoGEbNxtCZmZuqdtLtrghW"] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{ "id": 1, "method":"**blockchain.address.get_balance**", "params":["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 1, "method":"**blockchain.address.get_balance**", "params":["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }'; sleep 0.1) | ncat --**ssl** localhost 50002

    # Not implemented in Electrum-Client (only available with the Python-Server):

> (echo '{ "id": 1, "method":"**blockchain.address.get_mempool**", "params":["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 1, "method":"**blockchain.address.get_mempool**", "params":["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{ "id": 1, "method":"**blockchain.address.get_proof**", "params":["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 1, "method":"**blockchain.address.get_proof**", "params":["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{ "id": 1, "method":"**blockchain.utxo.get_address**", "params":["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 1, "method":"**blockchain.utxo.get_address**", "params":["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{ "id": 1, "method":"**blockchain.block.get_chunk**", "params":["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 1, "method":"**blockchain.block.get_chunk**", "params":["1NS17iag9jJgTHD1VXjvLCEnZuQ3rJDE9L"] }'; sleep 0.1) | ncat --**ssl** localhost 50002


**Subscribes**

> (echo '{"id": 1, "method": "**server.peers.subscribe**", "params": [] }'; sleep 0.1) | ncat localhost 50001

> (echo '{"id": 1, "method": "**server.peers.subscribe**", "params": [] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{"id": 1, "method": "**blockchain.numblocks.subscribe**", "params": [] }'; sleep 0.1) | ncat localhost 50001

> (echo '{"id": 1, "method": "**blockchain.numblocks.subscribe**", "params": [] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{ "id": 5, "method": "**blockchain.headers.subscribe**", "params": [] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 5, "method": "**blockchain.headers.subscribe**", "params": [] }'; sleep 0.1) | ncat --**ssl** localhost 50002

> (echo '{ "id": 5, "method": "**blockchain.address.subscribe**", "params": ["mjV6PrMG37EDouCQPHtm7bCdCeCpgMirUK"] }'; sleep 0.1) | ncat localhost 50001

> (echo '{ "id": 5, "method": "**blockchain.address.subscribe**", "params": ["mjV6PrMG37EDouCQPHtm7bCdCeCpgMirUK"] }'; sleep 0.1) | ncat --**ssl** localhost 50002


To Generate a new Keypair:
```bash
./new-keypair > keys.json
```

Assuming the following key pairs:

ABC/XYZ
```
Pub: 04e5c287de56be4aa59376f5c258ddc36f721e4d862a04e0b08178c736eb21f54ac4d2f5d6114270dc33d8a73203040c97d9afe136c81e9cb8bff09620919f4bde
Priv: 65e1112a6c7e6b0b62605834ab03bd50fedfe8a8dfcb0ccfe7fb21bb1870ee78
```

Android Client
```
Priv: 11c25bb3713ae465fe07c9a9bbb6321c2d175a4c4b3b67b9c59b62b868ab29de
Pub: 043833dccd886ce7fab5984820a224b132694e1527f1e0489461f48e2479ff29c1c59168382e85ce8d543c4ab1ecdc2eb96af6f0ad0d52d73131eb5a65961c08aa
```

To encrypt and send this message from the Android Client to ABC/XYZ:
```json
{
    "contract_type": "connection_request",
    "created_on": "02-06-2016",
    "expires_on": "02-01-2017",
    "email": "werner@io.co.za"
}
```

HOW TO ENCRYPT DATA: (NOTE: if `$to_encrypt` is JSON (which it should be) it must be escaped JSON i.e `addslashes($json_string)` )
SIG:
```bash
./encrypt "$xYZ_public_key" "$to_encrypt"
```
EXAMPLE:
```bash
./encrypt "04e5c287de56be4aa59376f5c258ddc36f721e4d862a04e0b08178c736eb21f54ac4d2f5d6114270dc33d8a73203040c97d9afe136c81e9cb8bff09620919f4bde" "{\"contract_type\": \"connection_request\", \"created_on\": \"02-06-2016\", \"expires_on\": \"02-01-2017\", \"email\": \"werner@io.co.za\"}"
```

RESULT:
```json
{
    "iv":"54ee174794bfd3328073974a9dac7f19",
    "ephemPublicKey":"04eab8186224ec94258f8fe4db75845c115f4a8bcd8890582559cac3c0284f2887000ec923d38415f70946706dda510e5d828a64aed9d130fc170525e90b53f87a",
    "ciphertext":"21a8386a7661e8e76e26ded00050aeebdbab54ffb18f02c512c38a2ddd5f3918102da5170fbd10baa9d0e08d81bd8fa0f7fd2bd3ebd97f66c49f8699b0e5134be59f8be773de6184232be11665102c221470a0d6809815dcb3242d6a30c8b81a7549d70262f938e1bddf1563fec0cca0a1d64f1e8ce8411498d1af4c3cf2e73a",
    "mac":"1b776235d8f2b3269fb4311b5450e975d3423e2685f702a019d76fa1e4aaa05b"
}
```

HOW TO SIGN: (NOTE: Param 2 of sign takes the public key of the recipient (XYZ) and the payload seperated by a pipe `|`)
SIG:
```bash
./sign "$android_private_key" "$xYZ_public_key|$encrypted_message"
```
EXAMPLE:
```bash
./sign "11c25bb3713ae465fe07c9a9bbb6321c2d175a4c4b3b67b9c59b62b868ab29de" "04e5c287de56be4aa59376f5c258ddc36f721e4d862a04e0b08178c736eb21f54ac4d2f5d6114270dc33d8a73203040c97d9afe136c81e9cb8bff09620919f4bde|{\"iv\":\"54ee174794bfd3328073974a9dac7f19\",\"ephemPublicKey\":\"04eab8186224ec94258f8fe4db75845c115f4a8bcd8890582559cac3c0284f2887000ec923d38415f70946706dda510e5d828a64aed9d130fc170525e90b53f87a\",\"ciphertext\":\"21a8386a7661e8e76e26ded00050aeebdbab54ffb18f02c512c38a2ddd5f3918102da5170fbd10baa9d0e08d81bd8fa0f7fd2bd3ebd97f66c49f8699b0e5134be59f8be773de6184232be11665102c221470a0d6809815dcb3242d6a30c8b81a7549d70262f938e1bddf1563fec0cca0a1d64f1e8ce8411498d1af4c3cf2e73a\",\"mac\":\"1b776235d8f2b3269fb4311b5450e975d3423e2685f702a019d76fa1e4aaa05b\"}"
```

RESULT:
```json
{
    "signature":"30440220537d2822f4c48aff43bbe333adbc33aa4c81a88c97ab67bf7005291468bf432a02206eab154e487890f1e4ea9bfc493904bfe264ac00c1d7f7cd1d76bf45d8a0611f"
}
```

HOW TO SEND:

SIG:
```
POST /v2/messages HTTP/1.1
Host: api.cnsnt.io
pubkey: $android_public_key
Content-Type: application/json
Cache-Control: no-cache
Postman-Token: 5d8ec8a5-f425-63e3-c1a7-6002b00a24b7

{
    "payload":"$xYZ_public_key|$encrypted_message",
    "signature":"$signature"
}

```
EXAMPLE:
```
POST /v2/messages HTTP/1.1
Host: api.cnsnt.io
pubkey: 043833dccd886ce7fab5984820a224b132694e1527f1e0489461f48e2479ff29c1c59168382e85ce8d543c4ab1ecdc2eb96af6f0ad0d52d73131eb5a65961c08aa
Content-Type: application/json
Cache-Control: no-cache
Postman-Token: 5d8ec8a5-f425-63e3-c1a7-6002b00a24b7

{
    "payload":"04e5c287de56be4aa59376f5c258ddc36f721e4d862a04e0b08178c736eb21f54ac4d2f5d6114270dc33d8a73203040c97d9afe136c81e9cb8bff09620919f4bde|{\"iv\":\"54ee174794bfd3328073974a9dac7f19\",\"ephemPublicKey\":\"04eab8186224ec94258f8fe4db75845c115f4a8bcd8890582559cac3c0284f2887000ec923d38415f70946706dda510e5d828a64aed9d130fc170525e90b53f87a\",\"ciphertext\":\"21a8386a7661e8e76e26ded00050aeebdbab54ffb18f02c512c38a2ddd5f3918102da5170fbd10baa9d0e08d81bd8fa0f7fd2bd3ebd97f66c49f8699b0e5134be59f8be773de6184232be11665102c221470a0d6809815dcb3242d6a30c8b81a7549d70262f938e1bddf1563fec0cca0a1d64f1e8ce8411498d1af4c3cf2e73a\",\"mac\":\"1b776235d8f2b3269fb4311b5450e975d3423e2685f702a019d76fa1e4aaa05b\"}",
    "signature":"30440220537d2822f4c48aff43bbe333adbc33aa4c81a88c97ab67bf7005291468bf432a02206eab154e487890f1e4ea9bfc493904bfe264ac00c1d7f7cd1d76bf45d8a0611f"
}
```

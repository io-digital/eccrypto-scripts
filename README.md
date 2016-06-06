
# eccrypto-scripts

##### testing

```bash
brew install bats
./tests
```

##### functions

```bash

# decrypt
./decrypt "$private_key" \
          "$input_vector" \
          "$ephemeral_public_key" \
          "$mac" \
          "$cipher_text" > decrypted.json

# or

echo $ciphertext | ./decrypt "$private_key" \
              "$input_vector" \
              "$ephemeral_public_key" \
              "$mac" > decrypted.json

# derive
./derive "$private_key" "$public_key" > shared_secret.json

# encrypt
cat my-message.txt | ./encrypt "$public_key" > encrypted.json
# or
./encrypt "$public_key" "$message" > encrypted.json

# sign
cat my-message.txt | ./sign "$private_key" > signature.json
# or
./sign "$private_key" "$message" > signature.json

# verify
cat my-message.txt | ./verify "$public_key" "$signature" > verification.json
# or
./verify "$public_key" \
         "$signature" \
         "$message" > verification.json

# new-keypair
./new-keypair > my-keypair.json
# or
./new-keypair --plain my-keypair.txt
```

##### all together now...

```bash
# this snippet requires jq (https://github.com/stedolan/jq)
read -r pub_a priv_a <<< $(./bin/new-keypair --plain)
read -r pub_b priv_b <<< $(./bin/new-keypair --plain)
./bin/encrypt $pub_a < file-from-b > encrypted_a
signable_a="${pub_a}|$(cat encrypted_a)"
read -r signature_b <<< $(echo $signable_a | ./bin/sign $priv_b | jq -r -c -M '.signature')
echo $signable_a | ./bin/verify $pub_b $signature_b
read -r iv_a ep_a ct_a mc_a <<< $(cat encrypted_a | jq -r -c -M '.iv, .ephemPublicKey, .ciphertext, .mac')
echo $ct_a | ./bin/decrypt $priv_a $iv_a $ep_a $mc_a
```

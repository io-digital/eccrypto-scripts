
# eccrypto-scripts

##### decrypt

```bash
./decrypt "$private_key" "$input_vector" "$ephemeral_public_key" "$mac" "$cipher_text"
# or
cat cipertext.txt | ./decrypt "$private_key" "$input_vector" "$ephemeral_public_key" "$mac"
```

##### derive

```bash
./derive "$private_key" "$public_key"
```

##### encrypt

```bash
cat my-message.txt | ./encrypt "$public_key"
# or
./encrypt "$public_key" "$message"
```

##### sign

```bash
cat my-message.txt | ./sign "$private_key"
# or
./sign "$private_key" "$message"
```

##### verify

```bash
cat my-message.txt | ./verify "$public_key" "$signature"
# or
./verify "$public_key" "$signature" "$message"
```

##### new-keypair

```bash
./new-keypair > my-keypair.txt
```

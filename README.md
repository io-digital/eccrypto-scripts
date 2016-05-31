
# eccrypto-scripts

##### decrypt
```bash
./decrypt "$private_key" "$input_vector" "$ephemeral_public_key" "$cipher_text" "$mac"
```

##### derive
```bash
./derive "$private_key" "$public_key"
```

##### encrypt
```bash
./encrypt "$public_key "$message"
```

##### sign
```bash
./sign "$private_key" "$message"
```

##### verify
```
./verify "$public_key" "$message" "$signature"
```

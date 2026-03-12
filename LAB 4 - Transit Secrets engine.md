### Step 1: Enable transit secrets engine (Encryption as a Service)
```bash
vault secrets enable transit
```

### Step 2: Create an encryption key
```bash
vault write -f transit/keys/my-app
```

### Step 3: Encryption/Decryption

1. Prepare data in base64
```bash
echo -n "sensitive data" | base64
```

2. Encrypt the data
```bash
vault write transit/encrypt/my-app plaintext=$(echo -n "sensitive data" | base64)
```

3. Decrypt data
```bash
vault write transit/decrypt/my-app ciphertext=<cipher text from previous command>
```

4. Decode the result:
```bash
echo "base64-output" | base64 -d 
```

### Step 4: Key rotation

1. Rotate the encryption key:
```bash
vault write -f transit/keys/my-app/rotate
```

2. Check key details:
```bash
vault read transit/keys/my-app
```

3. Encrypt new data with new key:
```bash
vault write transit/encrypt/my-app plaintext=$(echo -n "sensitive data" | base64)
```

### Step 5: Reencryption (rewrapping) old cipher text with new key

1. Reencrypt the data (without accessing the plaintext):
```bash
vault write transit/rewrap/my-app ciphertext="vault:v1:abcdefg..."
```

2. Decrypt data
```bash
vault write transit/decrypt/my-app ciphertext=<cipher text from previous command>
```

3. Decode the result
```bash
echo "base64-output" | base64 -d 
```
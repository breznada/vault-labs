### Step 1: Check what auth methods are enabled
```bash
vault auth list
```

### Step 2: Enable userpass auth method
```bash
vault auth enable userpass
```

### Step 3: Creating user
1. Create policy that will be attached to user
```bash
# Policy for database administrators - generating dynamic access credentials with admin privileges

vault policy write db-admin-policy - <<EOF
path "database/creds/admin-role" {
  capabilities = ["read"]
}
EOF
```

2. Create user with policy attached
```bash
vault write auth/userpass/users/AdamBreznen \
    password="password" \
    policies="db-admin-policy"
```

3. Login with user and test the credentials generation
```bash
# Unset the environment variable
unset VAULT_TOKEN

# Login as admin
vault login -method=userpass \
    username=AdamBreznen \
    password=password
    
# Test creds generation
vault read database/creds/admin-role
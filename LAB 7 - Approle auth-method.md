### Step 1: Enable approle auth method
```bash
vault auth enable approle
```

### Step 2: Create approle
Use the policy from previous lab
```bash
vault write auth/approle/role/my-app \
    token_policies="db-admin-policy" \
    token_ttl=1h \
    token_max_ttl=4h
```

### Step 3: Get role and secret_id
1. Get RoleID:
```bash
vault read auth/approle/role/my-app/role-id
```

2. Generate SecretID:
```bash
vault write -f auth/approle/role/my-app/secret-id
```

### Step 4: Login with approle and test access with the recieved token

1. Log in
```bash
vault write auth/approle/login \
    role_id="<role_id>" \
    secret_id="<secret_id>"
```

2. Store tokenx 
```bash
export VAULT_TOKEN="<token from login>"
```

3. Test permissions
```bash
vault read database/creds/admin-role
```
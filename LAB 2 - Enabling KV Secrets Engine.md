### Step 1: Enable KV secret engine
1. Enable KV v1 and v2 engines:
```bash
vault secrets enable -path=kv1 -version=1 kv
vault secrets enable -path=kv2 -version=2 kv
```

2. Verify newly mounted secrets engines:
```bash
vault secrets list
```

### Step 2: Basic operations - KV v1 secrets engine
```bash
# Write a secret
vault kv put kv1/app username=admin password=secret

# Read a secret
vault kv get kv1/app

# Update a secret
vault kv put kv1/app username=admin password=newsecret

# Read the updated secret
vault kv get kv1/app

# Delete a secret
vault kv delete kv1/app

# Read a secret
vault kv get kv1/app
```

### Step 3: Basic operations - KV v2 secrets engine
#### Basic operations

```bash
# Write a secret
vault kv put kv2/app username=admin password=secret

# Read a secret
vault kv get kv2/app

# Update a secret
vault kv put kv2/app username=admin password=newsecret

# Read the updated secret (notice it's now version 2)
vault kv get kv2/app

# Delete latest version
vault kv delete kv2/app

# Read the updated secret (notice only metadata is returned - but no data)
vault kv get kv2/app
```

#### KV v2 versioning

1. Create multiple versions:
```bash
vault kv put kv2/config api_key=v1
vault kv put kv2/config api_key=v2
vault kv put kv2/config api_key=v3
```

2. Read specific versions:
```bash
vault kv get -version=1 kv2/config
vault kv get -version=2 kv2/config
vault kv get -version=3 kv2/config
```

3. View version metadata:
```bash
vault kv metadata get kv2/config
```

4. Roll back to previous version:
```bash
vault kv rollback -version=1 kv2/config
```

5. Read the latest version (notice it's now the same as v1)
```bash
vault kv get kv2/config
```
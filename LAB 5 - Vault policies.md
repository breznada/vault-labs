Policies are used to define what actions users and entities can perform within Vault.

### Step 1: Creating read-only policy to read secrets from KV secrets engine

1. Create a new file called `readonly-kv.hcl` in your terminal with these contents:
```hcl
# Allow read-only access to secrets in the 'kv1' path
path "kv1/*" {
  capabilities = ["read", "list"]
}
```

2. Write this policy to Vault:
```bash
vault policy write readonly-kv readonly-kv.hcl
```

3. Verify the policy was created:
```bash
vault policy list
vault policy read readonly-kv
```

### Step 2: Testing the policy
To test the policy we need to authenticate to Vault with "entity" that has the policy attached. The most basic way to authenticate to Vault is using user token (just like we did with root token at the beggining of the labs).

1. Create token with the policy attached:
```bash
vault token create -policy=readonly-kv
```

2. Set the token to newly generated one:
```bash
export VAULT_TOKEN="<generated-token>"
```

3. Test the policy:
```bash
# These should work
vault kv get kv1/app
vault kv list kv1

# These should fail
vault kv put kv1/app password="pass"
vault kv delete kv1/app
```
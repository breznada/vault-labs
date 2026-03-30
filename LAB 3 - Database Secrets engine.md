### Step 1: Check what secrets engines are enabled
```bash
vault secrets list
```

### Step 2: Enable database secrets engine
```bash
vault secrets enable database
```

### Step 3: Connect Vault with the database
There is a PostgreSQL database running on the VM.

Connection details:
* Username: postgres
* Password: postgres
* Database: postgres
* URL: 127.0.0.1
* Port: 5432

1. To connect Vault with the DB run:
```bash
vault write database/config/my-postgres \
    allowed_roles="admin-role" \
    plugin_name="postgresql-database-plugin" \
    connection_url="postgresql://{{username}}:{{password}}@localhost:5432/postgres?sslmode=disable" \
    username="postgres" \
    password="postgres"
```

2. Check that the connection has been created
```bash
vault list database/config
```

3. Best practise is to rotate root credentials so there is no secret zero
```bash
vault write -f database/rotate-root/my-postgres
```

### Step 4: Create database role
For dynamic credentials generation, we need to specify how we want the credentials to be generated. This is done by setting Database role

1. Create the role
```bash
vault write database/roles/admin-role \
    db_name="my-postgres" \
    creation_statements="CREATE ROLE \"{{name}}\" WITH LOGIN PASSWORD '{{password}}' VALID UNTIL '{{expiration}}'; \
        GRANT SELECT ON ALL TABLES IN SCHEMA public TO \"{{name}}\";" \
    default_ttl="1h"
```

2. Try generating dynamic credentials using the role in the CLI
```bash
vault read database/creds/admin-role
```

3. Try generating dynamic credentials using the role in the UI

4. Try generating dynamic credentials using the role with the API
```bash
curl \
    --header "X-Vault-Token: $VAULT_TOKEN" \
    --request GET \
    ${VAULT_ADDR}/v1/database/creds/admin-role | jq
```bash

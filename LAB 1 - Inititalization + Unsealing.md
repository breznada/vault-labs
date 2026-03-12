### Step 1: Login to VM
```bash
ssh -p [SSH_Port] itzuser@[Remote_Host]
```

### Step 2: Vault status

1. Verify Vault's current status:
```bash
vault status
```

Example output:
```
[itzuser@itz-977nt3-helper-1 ~]$ vault status
Key                     Value
---                     -----
Seal Type               shamir
Initialized             false      - Vault is unitialized
Sealed                  true       - Vault is sealed 
Total Shares            0
Threshold               0
Unseal Progress         0/0
Unseal Nonce            n/a
Version                 1.21.3
Build Date              2026-02-03T14:56:30Z
Storage Type            raft
Removed From Cluster    false
HA Enabled              true
```

### Step 3: Initialize Vault

1. Initialize Vault
```bash
vault operator init \
    -key-shares=5 \
    -key-threshold=3
```

Example output:
```
Unseal Key 1: JYYF2BZtJC33AO6CxxjXKvqzuHMmP1VwNTAv5MnT8tNJ
Unseal Key 2: ijupbdiG+WMNFYePL8xlMPgxj7po6KXNW4QLKgRqmIdw
Unseal Key 3: PeGFqI/UXnxzNHNMu+sSq9RKFKZLM1JcD8oVA2nXRjzC
Unseal Key 4: SE89qfmuscKD6Y9Eu010TPf1BXP5FFvlZykimqDFUp/6
Unseal Key 5: l4in7kvPY0jCygzMRJUzSmQelo4BLaQlx8pWwUXGV+rc

Initial Root Token: hvs.1GgSjFBeNVmQFlSeA0FzN2K4
```

### Step 4: Unseal Vault

1. Unseal Vault

Run the unseal command 3 times, enter different key each time:
```bash
vault operator unseal
```
Example output:
```
[itzuser@itz-977nt3-helper-1 ~]$ vault operator unseal
Unseal Key (will be hidden): 
Key                     Value
---                     -----
Seal Type               shamir
Initialized             true
Sealed                  false
Total Shares            5
Threshold               3
```

2. Check the status again:
```bash
vault status
```

Example output:ww
```
[itzuser@itz-977nt3-helper-1 ~]$ vault status
Key                     Value
---                     -----
Seal Type               shamir
Initialized             true
Sealed                  false
Total Shares            5
Threshold               3
Version                 1.21.3
Build Date              2026-02-03T14:56:30Z
Storage Type            raft
Cluster Name            vault-cluster-3a42c9f9
Cluster ID              902e8c4c-0346-254d-6d75-edbe3ab31ada
Removed From Cluster    false
HA Enabled              true
HA Cluster              https://127.0.0.1:8201
HA Mode                 active
Active Since            2026-03-07T10:35:01.29334891Z
Raft Committed Index    37
Raft Applied Index      37
```

### Step 5: Login to Vault with command line
1. Copy the Initial Root Token from the `vault operator init` command and store it in VAULT_TOKEN env variable:
```bash
export VAULT_TOKEN=hvs.br2FanqpVkBIF3ELiDa99CMR
```

Or login directly with
```bash
vault login
```

3. Run a command to ensure you can interact with Vault:
```bash
vault secrets list
```

### Step 6: Login to Vault in UI
1. Open new terminal session
2. Forward port from the VM to localhost
```bash
ssh -L [Local_Port]:localhost:[Remote_Port] [User]@[SSH_Server] -p[SSH_Port]
```
Example command:
```bash
ssh -L 8200:localhost:8200 itzuser@149.81.41.123 -p 10022
```
3. Access the UI
http://localhost:8200
### Step 1: Enable the PKI Secrets Engine

1. First, enable a PKI secrets engine for the root CA:
```bash
vault secrets enable -path=pki_root pki
```

2. Configure the maximum lease TTL to 10 years:
```bash
vault secrets tune -max-lease-ttl=87600h pki_root
```

### Step 2: Generate Root CA

1. Generate and view the root certificate directly in the CLI:
```bash
vault write pki_root/root/generate/internal \
    common_name="Lab Root CA" \
    ttl=87600h
```

2. Configure the CA and CRL URLs:
```bash
vault write pki_root/config/urls \
    issuing_certificates="http://vault.lab:8200/v1/pki_root/ca" \
    crl_distribution_points="http://vault.lab:8200/v1/pki_root/crl"
```

### Step 3: Create Intermediate CA

1. Enable a PKI secrets engine for the intermediate CA:
```bash
vault secrets enable -path=pki_int pki
```

2. Set a shorter max TTL for the intermediate CA (5 years):
```bash
vault secrets tune -max-lease-ttl=43800h pki_int
```

3. Generate and view the intermediate CSR directly:
```bash
vault write pki_int/intermediate/generate/internal \
    common_name="Lab Intermediate CA" \
    ttl=43800h
```

4. Sign the intermediate CSR with the root CA and save to a file:
```bash
vault write -format=json pki_root/root/sign-intermediate \
    csr=@intermediate.csr \
    format=pem_bundle \
    ttl=43800h > signed_intermediate.json

cat signed_intermediate.json | jq -r .data.certificate > signed_intermediate.pem
```

5. Import the signed certificate back into the Vault Intermediate:
```bash
vault write pki_int/intermediate/set-signed \
    certificate=@signed_intermediate.pem
```

### Step 4: Create a Role for Issuing Certificates

1. Create a role for issuing certificates for a webapp:
```bash
vault write pki_int/roles/webapp \
    allowed_domains="lab.local" \
    allow_subdomains=true \
    max_ttl=720h \
    key_type="rsa" \
    key_bits=2048 \
    allowed_uri_sans="dns://lab.local" \
    require_cn=true \
    basic_constraints_valid_for_non_ca=true
```

### Step 5: Generate a Certificate for the webapp:

1. Generate and view a certificate directly in the CLI:
```bash
vault write pki_int/issue/webapp \
    common_name="webapp.lab.local" \
    ttl=72h
```

This will display:
- The certificate
- The private key (Vault will only output the private key once)
- CA chain
- Serial number
- Other metadata

2. Issue a certificate and save it to files (useful for deploying to services):
```bash
vault write -format=json pki_int/issue/webapp \
    common_name="webapp.lab.local" \
    ttl=72h > webapp_cert.json

# Extract each component to its own file
cat webapp_cert.json | jq -r .data.certificate > webapp_cert.pem
cat webapp_cert.json | jq -r .data.private_key > webapp_key.pem
cat webapp_cert.json | jq -r .data.ca_chain[] > webapp_ca_chain.pem

# [Optional] View each file to see the contents
cat webapp_cert.pem
cat webapp_key.pem
cat webapp_ca_chain.pem
```

### Step 6: Verify the Certificate

1. View the certificate details:
```bash
# For the CLI-generated certificate, copy the certificate content to a file first
# For the file-based certificate:
openssl x509 -in webapp_cert.pem -text -noout
```

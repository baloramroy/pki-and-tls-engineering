# SOP: Preparing SSL/TLS PEM Certificate for HAProxy

## 1. Objective

To prepare a combined PEM certificate file for HAProxy using the existing SSL certificate, private key, and intermediate CA certificates issued by Sectigo.

HAProxy requires a PEM file containing the server certificate, private key, and certificate chain to enable SSL/TLS termination.

## 2. Prerequisites

The following certificate files must be available:

| Filename                                                 | Description                                           |
| -------------------------------------------------------- | ----------------------------------------------------- |
| `yourdomain.key`                                         | Private key associated with the SSL certificate       |
| `STAR_yourdomain_com.crt`                                | Server certificate for `*.yourdomain.com`             |
| `SectigoPublicServerAuthenticationCADVR36.crt`           | Intermediate CA certificate                           |
| `SectigoPublicServerAuthenticationRootR46_USERTrust.crt` | Cross-signed CA certificate, if required by the chain |
| `USERTrustRSACertificationAuthority.crt`                 | USERTrust root CA certificate                         |

**Required tools:**

- Linux operating system
- OpenSSL
- HAProxy

## 3. Procedure

### Step 1: Create a Working Directory

Create a directory to keep all certificate files together.

```bash
mkdir -p /tmp/devops/haproxy-ssl
cd /tmp/devops/haproxy-ssl
```

Place all the required certificate files in this directory.

### Step 2: Verify the Certificate Files

Check whether the server certificate is already in PEM format.

```bash
head -n 2 STAR_yourdomain_com.crt
```

Expected output:

```text
-----BEGIN CERTIFICATE-----
```

Check the private key:

```bash
head -n 2 yourdomain.key
```

The output should start with either:

```text
-----BEGIN PRIVATE KEY-----
```

or:

```text
-----BEGIN RSA PRIVATE KEY-----
```

Files with `.crt` extensions can already be PEM-encoded. No conversion is necessary if they contain the PEM headers.

### Step 3: Verify the Private Key and Server Certificate

Confirm that the private key matches the server certificate.

Extract and calculate the server certificate public-key hash:

```bash
openssl x509 -in STAR_yourdomain_com.crt -pubkey -noout | openssl sha256
```

Extract and calculate the private key public-key hash:

```bash
openssl pkey -in yourdomain.key -pubout | openssl sha256
```

**Verification:**

Both SHA256 hashes must match.

If the hashes do not match, do not proceed with PEM generation.

### Step 4: Prepare the Certificate Chain

Create a separate file containing the intermediate CA certificates in the correct issuer order.

For the supplied certificate package:

```bash
cat SectigoPublicServerAuthenticationCADVR36.crt \
SectigoPublicServerAuthenticationRootR46_USERTrust.crt \
> intermediate-chain.pem
```

Note: Include the R46 certificate only if it is part of the verified chain. The self-signed USERTrust root certificate is normally not required in the HAProxy served chain.

### Step 5: Generate the Combined HAProxy PEM File

Combine the server certificate, private key, and intermediate CA chain into a single PEM file.

```bash
cat STAR_yourdomain_com.crt \
yourdomain.key \
intermediate-chain.pem \
> yourdomain_haproxy.pem
```

The generated file is:

```text
yourdomain_haproxy.pem
```

**PEM file structure:**

```text
-----BEGIN CERTIFICATE-----
Server Certificate
-----END CERTIFICATE-----

-----BEGIN PRIVATE KEY-----
Private Key
-----END PRIVATE KEY-----

-----BEGIN CERTIFICATE-----
Intermediate CA Certificate
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
Additional CA Certificate (if required)
-----END CERTIFICATE-----
```

### Step 6: Verify the Generated PEM File

- Identify all certificates
    
    ```bash
    openssl crl2pkcs7 -nocrl -certfile yourdomain_haproxy.pem | openssl pkcs7 -print_certs -noout
    ```
    
    Check that the certificates appear in this order:
    1. Domain certificate (STAR_yourdomain_com)
    2. Intermediate CA certificate
    3. Any additional intermediate CA certificates
    4. Root CA (USERTrustRSACertificationAuthority)

- Check the certificate and private-key entries:
    
    ```bash
    grep -n "BEGIN" yourdomain_haproxy.pem
    ```
    
    Expected entries should include:
    
    - Server certificate
    - Private key
    - Intermediate CA certificate(s)

- Verify that OpenSSL can read the server certificate from the generated PEM:

    ```bash
    openssl x509 -in yourdomain_haproxy.pem -noout -subject -issuer -dates
    ```

- Verify the private key:
    
    ```bash
    openssl pkey -in yourdomain_haproxy.pem -check -noout
    ```

### Step 7: Verify the Certificate Chain

Verify the certificate chain using the USERTrust root as the trust anchor.

```bash
openssl verify \
-CAfile USERTrustRSACertificationAuthority.crt \
-untrusted intermediate-chain.pem \
STAR_yourdomain_com.crt
```

Expected output:

```text
STAR_mynagad_com.crt: OK
```

If verification fails, inspect the Subject and Issuer fields of the CA certificates and confirm the correct certificate chain with Sectigo.

### Step 8: Set Appropriate File Permissions

Protect the generated PEM file because it contains the private key.

```bash
chmod 600 yourdomain_haproxy.pem
```

### Step 9: Deploy the PEM File to HAProxy

Copy the generated PEM file to the HAProxy certificate directory.

Example:

```bash
mkdir -p /etc/haproxy/certs
```

Copy the file:

```bash
cp yourdomain_haproxy.pem /etc/haproxy/certs/
```

Set ownership and permissions according to the HAProxy service account and deployment security policy.

## 4. HAProxy Configuration

Configure the HAProxy frontend to use the generated PEM file.

Example:

```haproxy
frontend https_frontend
    bind *:443 ssl crt /etc/haproxy/certs/yourdomain_haproxy.pem
    mode http
    default_backend application_backend
```

The `crt` parameter specifies the combined PEM certificate file.

## 5. Verification and Service Reload

- Before applying the configuration, validate the HAProxy configuration.
    
    ```bash
    haproxy -c -f /etc/haproxy/haproxy.cfg
    ```
    
    Expected output:
    
    ```text
    Configuration file is valid
    ```

If the configuration is valid, reload HAProxy using the appropriate service-management procedure.

- For systemd-based systems:

    ```bash
    systemctl reload haproxy
    ```

- Check service status:
    
    ```bash
    systemctl status haproxy
    ```

- Verify the deployed certificate:

    ```bash
    openssl s_client \
    -connect localhost:443 \
    -servername your-hostname.yourdomain.com \
    -showcerts
    ```

> Replace `your-hostname.yourdomain.com` with the actual hostname configured on HAProxy.

Check the certificate subject, issuer, and expiry date to confirm that the expected certificate is being served.

## 6. Final Output

The final generated file is:

```text
yourdomain_haproxy.pem
```

This file contains:

1. Server SSL certificate
2. Corresponding private key
3. Required intermediate CA certificate(s)

The combined PEM file is referenced directly in the HAProxy frontend configuration.

## 7. Important Security Notes

- Never share or expose the private key.
- Do not include the private key in email or unsecured file transfers.
- Maintain a secure backup of the original certificate and private key.
- Use appropriate file permissions for the combined PEM file.
- Do not include a root CA certificate in the served chain unless specifically required by the deployment.
- Always verify the certificate chain and private-key match before production deployment.
- Validate the HAProxy configuration before reloading the service.

---

**End of Document**

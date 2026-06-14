
# Apache HTTPS Practical Using Root CA → Sub CA → Server Certificate

## Step 1: Create Working Directory

```bash
mkdir (_project_name_)
cd (_project_name_)
```

---

# Root CA

## Generate Root CA Private Key

```bash
openssl genpkey -algorithm RSA -out (_root_ca_private_key_name_).key
```

Example:

```bash
openssl genpkey -algorithm RSA -out root.key
```

---

## Generate Root CA CSR

```bash
openssl req -new -key (_root_ca_private_key_name_).key -out (_root_ca_csr_name_).csr
```

Example:

```bash
openssl req -new -key root.key -out root.csr
```

Fill:

```text
Country Name: IN
State: Madhya Pradesh
Locality: Bhopal
Organization Name: (_organization_name_)
Common Name: (_root_ca_name_)
```

Example:

```text
Common Name: Root CA
```

---

## Generate Self-Signed Root Certificate

```bash
openssl x509 -req -in (_root_ca_csr_name_).csr -signkey (_root_ca_private_key_name_).key -out (_root_ca_certificate_name_).crt -days 3650
```

Example:

```bash
openssl x509 -req -in root.csr -signkey root.key -out root.crt -days 3650
```

---

## View Root Certificate

```bash
openssl x509 -in (_root_ca_certificate_name_).crt -text -noout
```

Example:

```bash
openssl x509 -in root.crt -text -noout
```

---

# Subordinate CA

## Generate Sub CA Private Key

```bash
openssl genpkey -algorithm RSA -out (_sub_ca_private_key_name_).key
```

Example:

```bash
openssl genpkey -algorithm RSA -out sub.key
```

---

## Generate Sub CA CSR

```bash
openssl req -new -key (_sub_ca_private_key_name_).key -out (_sub_ca_csr_name_).csr
```

Example:

```bash
openssl req -new -key sub.key -out sub.csr
```

Fill:

```text
Country Name: IN
State: Madhya Pradesh
Locality: Bhopal
Organization Name: (_organization_name_)
Common Name: (_sub_ca_name_)
```

Example:

```text
Common Name: Intermediate CA
```

---

## Sign Sub CA Certificate Using Root CA

```bash
openssl x509 -req -in (_sub_ca_csr_name_).csr -CA (_root_ca_certificate_name_).crt -CAkey (_root_ca_private_key_name_).key -CAcreateserial -out (_sub_ca_certificate_name_).crt -days 1825
```

Example:

```bash
openssl x509 -req -in sub.csr -CA root.crt -CAkey root.key -CAcreateserial -out sub.crt -days 1825
```

---

## View Sub CA Certificate

```bash
openssl x509 -in (_sub_ca_certificate_name_).crt -text -noout
```

Example:

```bash
openssl x509 -in sub.crt -text -noout
```

---

# Server Certificate

## Generate Server Private Key

```bash
openssl genpkey -algorithm RSA -out (_server_private_key_name_).key
```

Example:

```bash
openssl genpkey -algorithm RSA -out server.key
```

---

## Generate Server CSR

```bash
openssl req -new -key (_server_private_key_name_).key -out (_server_csr_name_).csr
```

Example:

```bash
openssl req -new -key server.key -out server.csr
```

Fill:

```text
Country Name: IN
State: Madhya Pradesh
Locality: Bhopal
Organization Name: (_organization_name_)
Common Name: (_domain_name_or_localhost_)
```

Examples:

```text
localhost
```

or

```text
example.com
```

---

## Sign Server Certificate Using Sub CA

```bash
openssl x509 -req -in (_server_csr_name_).csr -CA (_sub_ca_certificate_name_).crt -CAkey (_sub_ca_private_key_name_).key -CAcreateserial -out (_server_certificate_name_).crt -days 365
```

Example:

```bash
openssl x509 -req -in server.csr -CA sub.crt -CAkey sub.key -CAcreateserial -out server.crt -days 365
```

---

## View Server Certificate

```bash
openssl x509 -in (_server_certificate_name_).crt -text -noout
```

Example:

```bash
openssl x509 -in server.crt -text -noout
```

---

# Verify Certificate Chain

```bash
openssl verify -CAfile (_root_ca_certificate_name_).crt -untrusted (_sub_ca_certificate_name_).crt (_server_certificate_name_).crt
```

Example:

```bash
openssl verify -CAfile root.crt -untrusted sub.crt server.crt
```

Expected Output:

```text
server.crt: OK
```

---

# Apache Installation

## Update Packages

```bash
sudo apt update
```

---

## Install Apache and OpenSSL

```bash
sudo apt install apache2 openssl -y
```

---

## Enable SSL Module

```bash
sudo a2enmod ssl
```

---

## Restart Apache

```bash
sudo systemctl restart apache2
```

---

# Create SSL Directory

```bash
sudo mkdir -p /etc/apache2/ssl
```

---

# Copy Certificates

```bash
sudo cp (_server_certificate_name_).crt /etc/apache2/ssl/
sudo cp (_server_private_key_name_).key /etc/apache2/ssl/
sudo cp (_sub_ca_certificate_name_).crt /etc/apache2/ssl/
```

Example:

```bash
sudo cp server.crt /etc/apache2/ssl/
sudo cp server.key /etc/apache2/ssl/
sudo cp sub.crt /etc/apache2/ssl/
```

---

# Create Test Web Page

```bash
echo "<h1>HTTPS Enabled Successfully</h1>" | sudo tee /var/www/html/index.html
```

---

# Create SSL Virtual Host

```bash
sudo nano /etc/apache2/sites-available/ssl.conf
```

Paste:

```apache
<VirtualHost *:443>

    ServerName (_domain_name_or_localhost_)

    DocumentRoot /var/www/html

    SSLEngine on

    SSLCertificateFile /etc/apache2/ssl/(_server_certificate_name_).crt

    SSLCertificateKeyFile /etc/apache2/ssl/(_server_private_key_name_).key

    SSLCertificateChainFile /etc/apache2/ssl/(_sub_ca_certificate_name_).crt

</VirtualHost>
```

Example:

```apache
<VirtualHost *:443>

    ServerName localhost

    DocumentRoot /var/www/html

    SSLEngine on

    SSLCertificateFile /etc/apache2/ssl/server.crt

    SSLCertificateKeyFile /etc/apache2/ssl/server.key

    SSLCertificateChainFile /etc/apache2/ssl/sub.crt

</VirtualHost>
```

---

# Enable SSL Site

```bash
sudo a2ensite ssl.conf
```

---

# Check Apache Configuration

```bash
sudo apachectl configtest
```

Expected Output:

```text
Syntax OK
```

---

# Restart Apache

```bash
sudo systemctl restart apache2
```

---

# Check Apache Status

```bash
sudo systemctl status apache2
```

---

# Check HTTPS Port

```bash
sudo ss -tulpn | grep 443
```

---

# Test HTTPS

```bash
curl -k https://localhost
```

or open:

```text
https://localhost
```

---

# Useful Commands

## Show Private Key

```bash
openssl rsa -in (_server_private_key_name_).key -text -noout
```

---

## Show CSR

```bash
openssl req -in (_server_csr_name_).csr -text -noout
```

---

## Show Certificate

```bash
openssl x509 -in (_server_certificate_name_).crt -text -noout
```

---

## List Files

```bash
ls -lh
```

---

## Verify Server Certificate

```bash
openssl verify -CAfile (_root_ca_certificate_name_).crt -untrusted (_sub_ca_certificate_name_).crt (_server_certificate_name_).crt
```

---

# Certificate Flow

```text
(_root_ca_private_key_name_).key
        ↓
(_root_ca_csr_name_).csr
        ↓
(_root_ca_certificate_name_).crt

(_sub_ca_private_key_name_).key
        ↓
(_sub_ca_csr_name_).csr
        ↓
(_sub_ca_certificate_name_).crt

(_server_private_key_name_).key
        ↓
(_server_csr_name_).csr
        ↓
(_server_certificate_name_).crt
```

# Trust Chain

```text
Root CA
    ↓
Sub CA
    ↓
Server Certificate
    ↓
Apache HTTPS Website
```

# Apache Uses

```text
(_server_certificate_name_).crt
(_server_private_key_name_).key
(_sub_ca_certificate_name_).crt
```

# OpenSSL Security Lab

## RSA Encryption / Decryption

### Generate Private Key

```bash
openssl genpkey -algorithm RSA -out bank_pvt.pem
```

### Generate Public Key

```bash
openssl rsa -pubout -in bank_pvt.pem -out bank_pub.pem
```

### Encrypt File

```bash
openssl pkeyutl -encrypt -pubin -inkey bank_pub.pem -in cl1_msg.txt -out cl1_msg.enc
```

### Decrypt File

```bash
openssl pkeyutl -decrypt -inkey bank_pvt.pem -in cl1_msg.enc -out cl1_msg.dec
```

---

## AES Encryption / Decryption

### Generate Secret Key

```bash
openssl rand -base64 32 > secret.key
```

### Encrypt File

```bash
openssl enc -aes-256-cbc -salt -in message.txt -out message.txt.enc -pass file:./secret.key
```

### Decrypt File

```bash
openssl enc -d -aes-256-cbc -in message.txt.enc -out message_decrypted.txt -pass file:./secret.key
```

---

## Hashing (SHA-256)

### Generate Hash

```bash
openssl dgst -sha256 message.txt
```

### Save Hash to File

```bash
openssl dgst -sha256 message.txt > message.txt.sha256
```

---

## Digital Signature

### Generate Private Key

```bash
openssl genrsa -out private.pem 2048
```

### Generate Public Key

```bash
openssl rsa -in private.pem -pubout -out public.pem
```

### Sign File

```bash
openssl dgst -sha256 -sign private.pem -out message.txt.sig message.txt
```

### Verify Signature

```bash
openssl dgst -sha256 -verify public.pem -signature message.txt.sig message.txt
```

Expected Output:

```text
Verified OK
```

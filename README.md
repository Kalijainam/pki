
## RSA Encryption / Decryption

```bash
mkdir rsa
cd rsa

nano cl1_msg.txt
```

```text
Hello CDAC
```

```bash
openssl genpkey -algorithm RSA -out bank_pvt.pem

openssl rsa -pubout -in bank_pvt.pem -out bank_pub.pem

openssl pkeyutl -encrypt -pubin -inkey bank_pub.pem -in cl1_msg.txt -out cl1_msg.enc

openssl pkeyutl -decrypt -inkey bank_pvt.pem -in cl1_msg.enc -out cl1_msg.dec

cat cl1_msg.dec

cd ..
```

## AES Encryption / Decryption

```bash
mkdir aes
cd aes

nano message.txt
```

```text
Hello CDAC
```

```bash
openssl rand -base64 32 > secret.key

openssl enc -aes-256-cbc -salt -in message.txt -out message.txt.enc -pass file:./secret.key

openssl enc -d -aes-256-cbc -in message.txt.enc -out message_decrypted.txt -pass file:./secret.key

cat message_decrypted.txt

cd ..
```

## Hashing

```bash
mkdir hashing
cd hashing

nano message.txt
```

```text
Hello CDAC
```

```bash
openssl dgst -sha256 message.txt

openssl dgst -sha256 message.txt > message.txt.sha256

cat message.txt.sha256

cd ..
```

## Digital Signature

```bash
mkdir signature
cd signature

nano message.txt
```

```text
Hello CDAC
```

```bash
openssl genrsa -out private.pem 2048

openssl rsa -in private.pem -pubout -out public.pem

openssl dgst -sha256 -sign private.pem -out message.txt.sig message.txt

openssl dgst -sha256 -verify public.pem -signature message.txt.sig message.txt
```

Expected Output:

```text
Verified OK
```














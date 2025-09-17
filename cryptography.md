# Cryptography

## Public-key cryptography

### RSA with PKCS1.5

```sh
# Generate RSA key pair
ssh-keygen -f my-rsa-key -m “PEM”
```

```sh
# Convert public RSA key from OpenSSH format to PEM format
ssh-keygen -f my-rsa-key.pub -e -m “PEM” > my-rsa-key-pub.pem
```

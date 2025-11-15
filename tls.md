# TLS

## Certificate pinning

Resources:
- https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning
- https://www.codeproject.com/Articles/25487/Cryptographic-Interoperability-Keys
- https://developer.apple.com/news/?id=g9ejcf8y

The most common way to pin a public certificate, is to pin against the certificate's `subjectPublicKeyInfo` (Subject Public Key Info structure) field (often just abbreviated SPKI).
The `subjectPublicKeyInfo` is typically hashed using SHA-256, and then Base64-encoded. This value is then used when comparing agains certificates at run time (e.g while doing HTTP request over TLS).

If the certificate is in PEM format, you can calculate the hash with OpenSSL like this:

```shell
openssl x509 -in certificate.pem -pubkey -noout | openssl pkey -pubin -outform der | openssl dgst -sha256 -binary | openssl enc -base64
```

If the certificate is in DER format, you can calculate the hash with OpenSSL like this:

```shell
openssl x509 -in certificate.der -pubkey -noout -inform der | openssl pkey -pubin -outform der | openssl dgst -sha256 -binary | openssl enc -base64
```

## Creating a (Development) Certificate signed by a self-signed root CA (Certificate Authority) Certificate

Resources:
- [Igor Soarez: How to setup your own CA with OpenSSL](https://gist.github.com/Soarez/9688998)
- [Creating Certificates for TLS Testing](https://developer.apple.com/library/archive/technotes/tn2326/_index.html#//apple_ref/doc/uid/DTS40014136)
- [Generate an Azure Application Gateway self-signed certificate with a custom root CA](https://learn.microsoft.com/en-us/azure/application-gateway/self-signed-certificates)
- [Generate self-signed certificates](https://learn.microsoft.com/en-us/dotnet/core/additional-tools/self-signed-certificates-guide#with-openssl)

### Self signed root CA (Certificate Authority) Certificate

#### Generate a key pair for the self-signed root CA (Signing) Certificate

**Alternative 1: Generate a RSA key pair:**

```shell
openssl genrsa -out thomsmed.ca.key 2048
```

**Alternative 2: Generate a elliptic-curve (EC) key pair:**

```shell
openssl ecparam -out thomsmed.ca.key -genkey -name prime256v1
```

#### Generate a self-signed root CA (Signing) Certificate

**1: Generate a Certificate Signing Request (CSR)**

```shell
openssl req -new -sha256 -key thomsmed.ca.key -out thomsmed.ca.csr
```

> Note: This is the interactive way of generating a CSR, which is fine when generating the root CA Certificate.

**2: Generate a certificate using the CSR**

```shell
openssl x509 -req -sha256 -days 365 -in thomsmed.ca.csr -signkey thomsmed.ca.key -out thomsmed.ca.crt
```

### CA signed (Development) Certificate

#### Generate a key pair for the (Development) Certificate

**Alternative 1: Generate a RSA key pair:**

```shell
openssl genrsa -out localhost.key 2048
```

**Alternative 2: Generate a EC key pair:**

```shell
openssl ecparam -out localhost.key -genkey -name prime256v1
```

#### Generate a (Development) Certificate

**1: Generate a Certificate Signing Request (CSR)**

Interactivly:

```shell
openssl req -new -key localhost.key -out localhost.csr
```

Or using a configuration file:

> The `.conf` file specify the CSR. This is nice if you wish to generate a CSR for multiple sub domains (including .local domains!). If you don't specify -key, openssl wil generate one for you.

```shell
openssl req -new -config localhost.conf -key localhost.key -out localhost.csr
```

_Example config:_

```conf
# Specify the req seqtion for this config (openssl req)
[ req ]

utf8 = yes
default_bits = 2048
default_keyfile = localhost.key # Only used if the -key option is not present
encrypt_key = no
default_md = sha2
prompt = no

# This specifies the section containing the distinguished name fields to
# prompt for when generating a certificate or certificate request.
distinguished_name = req_distinguished_name

# This specifies the configuration file section containing a list of extensions
# to add to the certificate request. It can be overridden by the -reqexts
# command line switch. See the x509v3_config(5) manual page for details of the
# extension section format.
req_extensions = req_extensions

[ req_distinguished_name ]
C = NO
ST = Vestland
L = Bergen
O  = Localhost
OU = IT
CN = localhost

[ req_extensions ]
basicConstraints=CA:FALSE
subjectAltName=@subject_alt_names
subjectKeyIdentifier = hash

[ subject_alt_names ]
DNS.1 = localhost
DNS.2 = thomsmed.lan
```

Or using "inline" configuration file (using `list`):

```shell
openssl req -new -key localhost.key -out localhost.csr -config <( \
  echo '[ req ]'; \
  echo 'utf8 = yes'; \
  echo 'default_bits = 2048'; \
  echo 'encrypt_key = no'; \
  echo 'default_md = sha2'; \
  echo 'prompt = no'; \
  echo 'distinguished_name = req_distinguished_name'; \
  echo 'req_extensions = req_extensions'; \
  echo '[ req_distinguished_name ]'; \
  echo 'C = NO'; \
  echo 'ST = Vestland'; \
  echo 'L = Bergen'; \
  echo 'O  = Localhost'; \
  echo 'OU = IT'; \
  echo 'CN = localhost'; \
  echo '[ req_extensions ]'; \
  echo 'basicConstraints=CA:FALSE'; \
  echo 'subjectAltName=@subject_alt_names'; \
  echo 'subjectKeyIdentifier = hash'; \
  echo '[ subject_alt_names ]'; \
  echo 'DNS.1 = localhost'; \
  echo 'DNS.2 = thomsmed.lan')
```

**2: Generate (and sign) a certificate using the CSR**

**Alternative 1: Using the `x509` sub command:**

> About the `x509` sub command: This will generate a `.srl` file with a unique serial number given the signed certificate. This file gets overwriten with new unique serial numbers for each certificate signed, where the serial number is incremented. More info [here](https://gist.github.com/Soarez/9688998#signing).

Use with the -extensions option to add extensions from a CSR config file:

```shell
openssl x509 -req -sha256 -days 180 -in localhost.csr -CA thomsmed.ca.crt -CAkey thomsmed.ca.key -CAcreateserial -extfile localhost.conf -extensions req_extensions -out localhost.crt
```

Or use with the -extfile option to add extensions from dedicated extensions file:

```shell
openssl x509 -req -sha256 -days 180 -in localhost.csr -CA thomsmed.ca.crt -CAkey thomsmed.ca.key -CAcreateserial -extfile localhost.extensions.conf -out localhost.crt
```

_Example dedicated extension file:_

```conf
basicConstraints=CA:FALSE
subjectAltName=@subject_alt_names
subjectKeyIdentifier = hash

[ subject_alt_names ]
DNS.1 = localhost
DNS.2 = thomsmed.lan
```

**Alternative 2: Using the `ca` sub command:**

> About the `ca` sub command: The `ca` sub command takes a config file (`.conf`) used for signing the CSR. More info [here](https://gist.github.com/Soarez/9688998#openssl-ca).

```shell
openssl ca -config thomsmed.ca.conf -in localhost.csr -out localhost.crt
```

_Example config:_

```conf
# Specify the default ca section for this config (openssl ca)
default_ca = ca

[ ca ]

utf8 = yes
copy_extensions = copy

#  a text file containing the next serial number to use in hex. Mandatory.
#  This file must be present and contain a valid serial number.
#  Starts at 01 (openssl automatically increment this)
serial = ./serial.txt

# the text database file to use. Mandatory. This file must be present though
# initially it will be empty.
database = ./database.txt

# specifies the directory where new certificates will be placed. Mandatory.
new_certs_dir = ./newcerts

# the file containing the CA certificate. Mandatory
certificate = ./thomsmed.ca.crt

# the file contaning the CA private key. Mandatory
private_key = ./thomsmed.ca.key

# the message digest algorithm. Remember to not use MD5
default_md = sha1

# for how many days will the signed certificate be valid
default_days = 365

# a section with a set of variables corresponding to DN fields
policy = ca_policy

[ ca_policy ]
# if the value is "match" then the field value must match the same field in the
# CA certificate. If the value is "supplied" then it must be present.
# Optional means it may be present. Any fields not mentioned are silently
# deleted.
countryName = match
stateOrProvinceName = supplied
localityName = supplied
organizationName = supplied
organizationalUnitName = supplied
commonName = supplied
```

**Optional: Verify the newly created Certificate:**

```shell
openssl x509 -in localhost.crt -text -noout
```

### Installing a self-signed root CA (Certificate Authority) Certificate

The simplest way is to email the cert, and then open the email om the devices you wish to install it on.

### Renewing Certificates

It's easy! Just run the `x509` or the `ca` sub command again with the **same** private key.

## Misc

### Generate a PKCS12 file

A PKCS12 file is commonly used to bundle a private key together with it's x509 certificate (it's an archive file format)
Generate PKCS12 file:
> NB! You need to specify a export password! If the private key you pass inn is password protected, you can pass in the password with the `-passin` option.

```shell
openssl pkcs12 -export -inkey localhost.key -in localhost.crt -out localhost.pfx
```

Verify PKCS12 file:

```shell
openssl pkcs12 -in localhost.pfx -info
```

### Extract .key and .crt from PKCS12 file

```shell
# Extract private key
openssl pkcs12 -in localhost.pfx -nocerts -out localhost.key

# Extract public key/certificate
penssl pkcs12 -in localhost.pfx -clcerts -nokeys -out localhost.crt

# Decrypt private key
openssl rsa -in localhost.key -out localhost-decrypted.key
```

### Convert PEM certificate file to DER certificate file

> Android for example requires DER certificate files.

```shell
openssl x509 -inform PEM -outform DER -in thomsmed.ca.crt -out thomsmed.ca.der
```

### Convert PFX certificate file to PEM certificate file

> gRPC Swift / SwiftNIO for example requires PEM certificate files.

```shell
openssl pkcs12 -in localhost.pfx -out localhost.pem -nodes
```

### Inspect a RSA key-file:

```shell
openssl rsa -in thomsmed.ca.key -noout -text
```

### Extract the public key from RSA key-file:

```shell
openssl rsa -in thomsmed.ca.key -pubout -out thomsmed.ca.pubkey
```

### Inspect a CSR (Certificate Signing Request):

```shell
openssl req -in localhost.csr -noout -text
```

### Inspect a x509 certificate:

```shell
openssl x509 -in localhost.crt -noout -text
```

### Verify a signed certificate:

```shell
openssl verify -CAfile thomsmed.ca.crt localhost.crt
```

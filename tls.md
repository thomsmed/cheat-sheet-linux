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

## Generate a PKCS12 file

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

## Extract .key and .crt from PKCS12 file

```shell
# Extract private key
openssl pkcs12 -in localhost.pfx -nocerts -out localhost.key

# Extract public key/certificate
penssl pkcs12 -in localhost.pfx -clcerts -nokeys -out localhost.crt

# Decrypt private key
openssl rsa -in localhost.key -out localhost-decrypted.key
```

## Convert PEM certificate file to DER certificate file

> Android for example requires DER certificate files.

```shell
openssl x509 -inform PEM -outform DER -in thomsmed.ca.crt -out thomsmed.ca.der
```

## Convert PFX certificate file to PEM certificate file

> gRPC Swift / SwiftNIO for example requires PEM certificate files.

```shell
openssl pkcs12 -in localhost.pfx -out localhost.pem -nodes
```

## Installing a self signed root CA (Certificate Authority) Certificate

The simplest way is to email the cert, and then open the email om the devices you wish to install it on.

## TLS Certificates for development

Credit to this awesome guide by [Igor Soarez](https://gist.github.com/Soarez/9688998).
> Note to self: Already generated CA cert is stored in KeyStore

### Self signed root CA (Certificate Authority) Certificate

Generate a RSA key pair and store it in `thomsmed.ca.key`:
> This key pair is used when creating the root CA Signing Certificate.

```shell
openssl genrsa -out thomsmed.ca.key 2048
```

Generate the Self Signed root CA Signing Certificate:

```shell
openssl req -new -x509 -key thomsmed.ca.key -out thomsmed.ca.crt -days 365 -sha256
```

### Generate Keys and CSR (Certificate Signing Request) for localhost

Generate a RSA key pair and store it in `localhost.key`:
> This key pair is used when creating the CSR for localhost

```shell
openssl genrsa -out localhost.key 2048
```

Generate a CSR (interactivly):
> The req sub command brings up an interactive shell to help fill out the CSR

```shell
openssl req -new -key localhost.key -out localhost.csr
```

Generate a CSR with CSR config file:
> The `.conf` file specify the CSR. This is nice if you wish to generate a CSR for multiple sub domains (Also usefull for .local domains!!!). If you don't specify -key, openssl wil generate one for you. More info [here](https://gist.github.com/Soarez/9688998#generate-keys-and-certificate-signing-request-csr)

```shell
openssl req -new -out localhost.csr -config localhost.conf -key localhost.key
```

Example config:

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


# this specifies the configuration file section containing a list of extensions
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

### Signing the localhost CSR (Certificate Signing Request)

Using the `x509` sub command:
> This will generate a `.srl` file with a unique serial number given the signed certificate. This file gets overwriten with new unique serial numbers for each certificate signed, where the serial number is incremented.
Use with the -extensions option to add extensions from CSR config file:

```shell
openssl x509 -req -in localhost.csr -CA thomsmed.ca.crt -CAkey thomsmed.ca.key -CAcreateserial -extfile localhost.conf -extensions req_extensions -out localhost.crt -days 365 -sha256
```

Or use with the -extfile option to add extensions from dedicated extensions file:

```shell
openssl x509 -req -in localhost.csr -CA thomsmed.ca.crt -CAkey thomsmed.ca.key -CAcreateserial -extfile localhost.extensions.conf -out localhost.crt -days 365 -sha256
```

Example extension file:

```conf
basicConstraints=CA:FALSE
subjectAltName=@subject_alt_names
subjectKeyIdentifier = hash

[ subject_alt_names ]
DNS.1 = localhost
DNS.2 = thomsmed.lan
```

Using the `ca` sub command:
> The `ca` sub command takes a config file (`.conf`) used for signing the CSR. More info [here](https://gist.github.com/Soarez/9688998#openssl-ca)

```shell
openssl ca -config thomsmed.ca.conf -in localhost.csr -out localhost.crt
```

Example config:

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

### Renewing certificates

It's easy! Just run the x509 sub command again with the **same** private key.

### For fun

Inspect a RSA key-file:

```shell
openssl rsa -in thomsmed.ca.key -noout -text
```

Extract the public key from RSA key-file:

```shell
openssl rsa -in thomsmed.ca.key -pubout -out thomsmed.ca.pubkey
```

Inspect a CSR (Certificate Signing Request):

```shell
openssl req -in localhost.csr -noout -text
```

Inspect a x509 certificate:

```shell
openssl x509 -in localhost.crt -noout -text
```

Verify a signed certificate:

```shell
openssl verify -CAfile thomsmed.ca.crt localhost.crt
```

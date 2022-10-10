# GPG - GNU Privacy Guard

Complete and free implementation of the OpenPGP standard (also known as PGP - Pretty Good Privacy).

## Use cases

Use GPG keys to sign your git commits
```bash
# Also remember to add the public key of your GPG key pair to your remote CVM service (like GitHub)
git config --global user.signingkey <gpg-key-id> # E.g git config --global user.signingkey 3AA5C34371567BD2
```

Encrypt files
```bash
# Add multiple --recipent flags to add to the list of people able to decrypt the file
gpg --output some-file.txt.gpg --encrypt --recipient recipent@email.com some-file.txt
```

Decrypt files
```bash
gpg --output some-file.txt --decrypt some-file.txt.gpg

# Decrypt files encrypted with a symmetric key
gpg --output some-file.txt.gpg --symmetric some-file.txt
```

## GPG Key pair management

Install latest GPG suite at https://gnupg.org/download/index.html

List localy available GPG key pair
> The GPG key id is the second part of the 'sec' value of the GPG key pair (after the '/')
```bash
gpg --list-secret-keys --keyid-format=long
```

Generate new key pair:
```bash
gpg --full-generate-key
```

View the public key of the GPG key pair
```bash
# Prints the public GPG key in ASCII armor format
# The GPG key id is the second part of the 'sec' value of the GPG key pair (after the '/')
$ gpg --armor --export <gpg-key-id> # E.g gpg --armor --export 3AA5C34371567BD2
```

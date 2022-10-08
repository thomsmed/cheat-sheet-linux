# GPG - GNU Privacy Guard

Complete and free implementation of the OpenPGP standard (also known as PGP - Pretty Good Privacy).

## Use cases

Use GPG keys to sign your git commits
```bash
# Also remember to add the public key of your GPG key pair to your remote CVM service (like GitHub)
git config --global user.signingkey <gpg-key-id> # E.g git config --global user.signingkey 3AA5C34371567BD2
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
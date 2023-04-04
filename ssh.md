# SSH - OpenSSH remote login client

```bash
# Generate new ssh key pair (RSA is default)
ssh-keygen -f ~/.ssh/raspberrypi4-ssh-key
# With specified username
ssh-keygen -f ~/.ssh/raspberrypi4-ssh-key -C thomsmed

# Add key to ssh agent
ssh-add ~/.ssh/raspberrypi4-ssh-key

# Copy the public key into 'authorized_keys' on the remote computer
# Manually copy:
cat ~/.ssh/raspberrypi4-ssh-key.pub | ssh pi@10.0.0.10 "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys"

# Using ssh-copy-id
ssh-copy-id -i ~/.ssh/raspberrypi4-ssh-key pi@10.0.0.10
```

## Add key to ssh config

Keys added to the ssh config with `AddKeysToAgent yes` are automatically added to the ssh agent on startup. Otherwise the key get gets added when used.

```conf
# ~/.ssh/config

Host *
  AddKeysToAgent yes
  IdentityFile ~/.ssh/id_rsa
  
Host github.com
  HostName github.com
  AddKeysToAgent yes
  IdentityFile ~/.ssh/github-ssh-key

Host RaspberryPi4
  IgnoreUnknown UseKeychain
  # Search the KeyChain for private key passphrase (if the key is protected by a passphrase), and store the passphrase in KeyChain if it is not already there (MacOS specific):
  UseKeychain yes
  HostName 192.168.10.167
  User pi
  IdentityFile ~/.ssh/raspberrypi4-ssh-key
```

Examples:

```shell
# Connect to the RaspberryPi4 host
ssh RaspberryPi4
```

## Restart SSH Agent

```shell
# Kill current SSH Agent, then start it again and configures environment variables by evaluating the output from 'ssh-agent -s'
ssh-agent -k && eval $(ssh-agent -s)
```

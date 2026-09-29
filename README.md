# Kamiak Guide

This repo exists as an extended guide for understanding and using Kamiak.

## Kamiak Architecture

login box is basically a bastion server
create and show drawing comparison

## SSH Config with Key Access

***Never*** share your private key to anyone for any reason. The `.pub` file is meant to be shared.

```
# example private key file
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gt
ZWQyNTUxOQAAACAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
-----END OPENSSH PRIVATE KEY-----

# example pub key file
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBKmE91MTACCxpMz6X8ZbtDtvZwnCRGLc0QcQ9nEnyKn your_email@example.com
```

### creating a private and public key pair
```
ssh-keygen -t ed25519 -f ~/.ssh/YOUR_NAME_YEAR_key -C "YOUR_EMAIL@wsu.edu"

# example
ssh-keygen -t ed25519 -f ~/.ssh/jeremy_2026_key -C "jeremy.banks@wsu.edu"
```

### creating a ssh config
```
mkdir -p ~/.ssh
touch ~/.ssh/config
chmod 600 ~/.ssh/config
```

### example config for GitHub and Kamiak
```
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/jeremy_2026_key
    IdentitiesOnly yes

Host kamiak
    HostName kamiak.wsu.edu
    User jeremy.banks
    IdentityFile ~/.ssh/jeremy_2026_key
    IdentitiesOnly yes
```

### sharing your pub file with kamiak
```
ssh-copy-id -i ~/.ssh/YOUR_NAME_2026.pub kamiak
```

### Rotating 

Key pairs are not meant to be used indefinitely! National Institute of Standards and Technology (NIST) [recommends](https://csrc.nist.gov/pubs/sp/800/57/pt1/r5/final):

`... a maximum cryptoperiod of about one to three years is recommended. A private signature key shall be destroyed at the end of its cryptoperiod.`

Simply follow the instructions again for creating a key, this time with 2027

Delete the old private key `rm ~/.ssh/jeremy_2026_key`

## IDE Recommendations

- Mac
- Windows
- Linux

## Using Git

creating a branch
using a branch (commit and push)
merging branches
branch strategy
managing secrets


## GitHub Runner

i want to set up some kind of cicd for github to run jobs from github into a 'runner' on kamiak
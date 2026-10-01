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

## Windows WSL > PuTTY

PuTTY is unneccessary for SSH now that [Windows Subsystem for Linux (WSL)](https://learn.microsoft.com/en-us/windows/wsl/install) supports installation of Ubuntu.

WSL does not support virtual environments like KVM/QEMU or Docker, but is otherwise a nearly fully-featured "Linux Shell on Windows".

## Using Git

creating a branch
using a branch (commit and push)
merging branches
branch strategy
managing secrets


## GitHub Runner

Shen Labs uses GitHub for codebase storage, and GitHub features an automation solution called GitHub Actions. When code is pushed to a repo in GitHub, a set of commands can automatically be executed on Kamiak.

1. ***NEVER*** run CPU intense jobs on the login node!
1. Escalated priviledge is not available on Kamiak. 

### Example YAMLs
`.github/workflows/login_node.yml`

`.github/workflows/slurm_job.yml`

`.github/workflows/slurm_job_tty.yml`


# If you try, others will stop you

I made a github runner today, and it was killed.
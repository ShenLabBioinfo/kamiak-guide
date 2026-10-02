# Kamiak Guide

This repo exists as an extended guide for understanding and using Kamiak.

## Kamiak Architecture

login box is basically a bastion server
create and show drawing comparison

## SSH Config with Key Access

***Never*** share your private key to anyone for any reason.

The `.pub` file is meant to be shared.

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

### example ~/.ssh/config
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

Host kamiak-p3n01
    HostName login-p3n01.kamiak.wsu.edu
    User jeremy.banks
    IdentityFile ~/.ssh/jeremy_key
    IdentitiesOnly yes

Host kamiak-p3n02
    HostName login-p3n02.kamiak.wsu.edu
    User jeremy.banks
    IdentityFile ~/.ssh/jeremy_key
    IdentitiesOnly yes

Host kamiak-p3n03
    HostName login-p3n03.kamiak.wsu.edu
    User jeremy.banks
    IdentityFile ~/.ssh/jeremy_key
    IdentitiesOnly yes
```

### sharing your pub file with kamiak
```
ssh-copy-id -i ~/.ssh/YOUR_NAME_2026.pub kamiak
ssh-copy-id -i ~/.ssh/YOUR_NAME_2026.pub kamiak-p3n01
ssh-copy-id -i ~/.ssh/YOUR_NAME_2026.pub kamiak-p3n02
ssh-copy-id -i ~/.ssh/YOUR_NAME_2026.pub kamiak-p3n03
```

### Rotating 

Key pairs are not meant to be used indefinitely! National Institute of Standards and Technology (NIST) [recommends](https://csrc.nist.gov/pubs/sp/800/57/pt1/r5/final):

`... a maximum cryptoperiod of about one to three years is recommended. A private signature key shall be destroyed at the end of its cryptoperiod.`

Simply follow the instructions again for creating a key, this time using 2027 as the year. Delete the old private key `rm ~/.ssh/jeremy_2026_key`

## Windows WSL > PuTTY

Although the Kamiak Quick Guides mention this, I think it bears repeating. PuTTY is unneccessary for SSH now that [Windows Subsystem for Linux (WSL)](https://learn.microsoft.com/en-us/windows/wsl/install) supports installation of Ubuntu.

WSL does not support virtual environments like KVM/QEMU or Docker, but is otherwise a nearly fully-featured "Linux Shell on Windows".

## Using Git

creating a branch
using a branch (commit and push)
merging branches
branch strategy
managing secrets


## GitHub Runner

Shen Labs uses GitHub for codebase storage, and GitHub features an automation solution called GitHub Actions. When code is pushed to a repo in GitHub, a set of commands can automatically be executed on Kamiak.

1. GitHub Actions are executed using **your WSU Kamiak credentials**, so...
   1. Do not add collaborators to repos with your GitHub Runner
   1. Do not share your GitHub Runner with anyone or use anyone else's GitHub Runner!
   1. Disable `Pull Request` in Settings -> General -> Features to prevent others from potentially executing code on your runners (you would still have to accept their PR, but it is still a threat vector).
1. ***NEVER*** run CPU intense jobs on the login node!
1. Escalated priviledge is not available on Kamiak. 

### Start/Stop the Runner

I recommend `nohup` for the purpose of keeping GitHub Runner service active, `tmux` works as well but resuming an interactive shell may be unneccessary.

`nohup bash -c 'while true; do echo "Runner starting: $(date +%Y-%m-%d-%H%M)"; ./run.sh; sleep 10; done' > "nohup-$(date +%Y-%m-%d-%H%M).log" 2>&1 &`

The above command will start nohup bash session, then a loop which starts the GitHub Runner, sleeps for 10 seconds if the Runner process ends, and then repeats the loop. Log files for tracing the events of nohup will be stored in `github-runner/nohup-YYYY-MM-DD-HHmm.log`, while the logs for GitHub Runner are stored in the `github-runner/_diag` directory of the GitHub Runner.

```
√ Connected to GitHub

2026-10-02 05:02:50Z: Runner reconnected.
Current runner version: '2.337.0'
2026-10-02 05:02:50Z: Listening for Jobs
```

To stop the runner you can run `pkill -u "$USER" -x Runner.Listener`

### Troubleshooting the Runner
- Don't see your Runner.Listener process in top but know you ran it?
- Is GitHub stating the Runner is :green_circle: Idle?
- Is your nohup log stating "A session for this runner already exists."?

Remember that Kamiak has multiple login nodes! The example ~/.ssh/config in this document shows you how to log into whichever specific node might be running the process.

### Example YAMLs

`.github/workflows/login_node.yml`
This example shows a job running on the same login node that is runninng GitHub Runner.

```
name: hello world login node

on:
  push:
    branches:
      - '**'

jobs:
  hello:
    runs-on: self-hosted
    steps:
      - run: echo "hello world from $(hostname)"
```

`.github/workflows/srun.yml`
foo bar

```
name: Hello World SLURM

on:
  push:
    branches:
      - '**'

jobs:
  slurm:
    runs-on: self-hosted
    steps:
      - name: Submit SLURM job
        run: |
          srun --job-name=github-actions \
               --time=00:05:00 \
               --ntasks=1 \
               --cpus-per-task=1 \
               bash -lc 'echo "hello world from $(hostname)"'
```

`.github/workflows/idev.yml`
foo bar

```
name: Run Interactive IDEV

on:
  push:
    branches:
      - '**'

jobs:
  idev:
    runs-on: self-hosted
    steps:
      - name: Start IDEV and run commands
        run: |
          idev bash -lc '
            echo "hello world from $(hostname)"
            exit
          '
```

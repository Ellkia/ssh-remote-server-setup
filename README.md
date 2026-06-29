# SSH Remote Server Setup

Project: https://roadmap.sh/projects/ssh-remote-server-setup

## Description
Setting up a remote Linux server on GCP with SSH configuration and basic security hardening.

## Steps

### 1. VM Creation
- Provider: Google Cloud Platform
- Machine type: e2-micro
- OS: Ubuntu 22.04 LTS x86/64
- Zone: europe-west1-b

### 2. Generating SSH Key Pairs
Generated two SSH key pairs on the local machine:

```bash
ssh-keygen -t ed25519 -C "cle-principale" -f ~/.ssh/gcp_key_1
ssh-keygen -t ed25519 -C "cle-secondaire" -f ~/.ssh/gcp_key_2
```

Public keys were added to the GCP VM metadata to allow SSH access.

### 3. Connecting to the Server
```bash
# Using first key
ssh -i ~/.ssh/gcp_key_1 cle-principale@<SERVER_IP>

# Using second key
ssh -i ~/.ssh/gcp_key_2 cle-secondaire@<SERVER_IP>
```

### 4. SSH Config File
Configured `~/.ssh/config` on the local machine to allow connection via alias:

Host server-principal
HostName <SERVER_IP>
User cle-principale
IdentityFile ~/.ssh/gcp_key_1

Host server-secondaire
HostName <SERVER_IP>
User cle-secondaire
IdentityFile ~/.ssh/gcp_key_2

This allows connecting with:
```bash
ssh server-principal
ssh server-secondaire
```

### 5. Security Hardening

#### Disable password authentication
Edited `/etc/ssh/sshd_config` and set:

PasswordAuthentication no

Then restarted SSH:
```bash
sudo systemctl restart ssh
```

#### Install and configure fail2ban
```bash
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

fail2ban automatically bans IPs that repeatedly fail SSH authentication, protecting against brute force attacks.

## Key Learnings
- How SSH key-based authentication works vs password authentication
- Defense in depth: multiple security layers (keys + passphrase + fail2ban + PasswordAuthentication disabled)
- How to manage multiple SSH identities via ~/.ssh/config

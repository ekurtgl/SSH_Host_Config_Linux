# SSH_Host_Config_Linux
SSH host configuration steps needed in Linux for secure connection with password-protected private key only. Username & password login is not allowed.

## Pre-requisites
Install openssh on the server (e.g., workstation):
```
sudo apt update
sudo apt install openssh-server
```
Install openssh on the client (e.g., laptop):
```
sudo apt update
sudo apt install openssh-client
```

## Step 1: Generate an SSH Key Pair
If you don’t already have a key pair, generate one on your client machine (e.g., your laptop), and make sure you enter a strong password:
```bash
ssh-keygen
```
Private Key: Stored locally (default: `~/.ssh/id_rsa`).
Public Key: Stored in `~/.ssh/id_rsa.pub`.

## Step 2: Copy the Public Key to the Server Machine
Use the `ssh-copy-id` command to copy your public key to the server:

`ssh-copy-id <user_name>@<server_ip>`

Alternatively, manually append the public key to the server's `~/.ssh/authorized_keys` file:
1. Connect to the server:

`ssh <user_name>@<server_ip>`

2. Create the ~/.ssh directory under your home directory if it doesn't exist:
```
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```
3. Append your public key to authorized keys list:
```
echo "your_public_key_contents" >> ~/.ssh/authorized_keys
```
or copy and paste the content in the public key file to the authorized_keys file. If the file doesn't exist you can create it via:
```
sudo touch authorized_keys
```

## Step 3: Disable Password Authentication and Remote Root Login
1. Modify the SSH configuration file on the server to allow only private key-based authentication:
Open the SSH configuration file:
```
sudo nano /etc/ssh/sshd_config
```
2. Update or add the following lines:
```
PasswordAuthentication no
PubkeyAuthentication yes
PermitRootLogin no
```
3. Save and exit.

## Step 4: Restart the SSH Service
Apply the changes by restarting the SSH service:
```
sudo systemctl restart ssh
```
## Step 5: Test Key-Based Authentication
From your client machine, test logging in to the server:
```
ssh -i ~/.ssh/id_rsa <user_name>@<server_ip> # this should work
ssh -i ~/.ssh/id_rsa.pub <user_name>@<server_ip> # this should not work
```

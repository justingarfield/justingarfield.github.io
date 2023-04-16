# Important Linux Files and Folders

* User/Home Related
  * `~/.ssh/authorized_keys` - Contains the public keys that are allowed for certificate-based SSH connectivity

# Install PowerShell for Linux

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/microsoft-ubuntu-22_04-prod.gpg

sudo curl -o /etc/apt/sources.list.d/microsoft.list https://packages.microsoft.com/config/ubuntu/22.04/prod.list
sudo apt-get update
sudo apt-get install -y powershell

pwsh --version
```

# Install Azure CLI

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/microsoft-ubuntu-22_04-prod.gpg

AZ_REPO=$(lsb_release -cs)
echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | sudo tee /etc/apt/sources.list.d/azure-cli.list

sudo apt-get install apt-transport-https
sudo apt-get update && sudo apt-get install azure-cli

az --version
```

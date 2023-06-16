## Installation

### macOS

```zsh
brew install gnupg gnupg2
```

### Windows

* https://www.gpg4win.org/index.html

### Linux

```bash
sudo apt update
sudo apt install gnupg2
```

## Create new keypair

```shell
gpg --full-generate-key
```

* Use RSA / RSA + 4096-bit
* Use 2yr expire

## Other

```shell
gpg --list-keys              # See all public keys
gpg --list-key [your@email]  # See just your public key
gpg --list-secret-key        # See your own private key
gpg --send-key [keyID]       # Upload public key to key server
```

# SSH Stuff

## Generate new SSH Keypair

Format: `ssh-keygen -a <number of KDF> -b <bit-length> -C <comment> -f <output filename> -t <key type>`

### RSA Example

`ssh-keygen -a 64 -b 4096 -C jgarfield@thehive.jgarfield.com -f ~/.ssh/jgarfield_thehive_jgarfield_com_rsa -t rsa`

### ED25519 Example for GitHub

`ssh-keygen -a 64 -C accounts@jgarfield.com -f ./github_accounts_jgarfield_com_ed25519 -t ed25519`

## Copy Public Key to Remote Machine

`ssh-copy-id -i ~/.ssh/id_rsa.pub user@77.134.54.101`

## Adding your SSH key to the ssh-agent

* `eval "$(ssh-agent -s)"` - Start SSH Agent
* `ssh-add <private key filename>`

# References

* [How many KDF rounds for an SSH key?](https://crypto.stackexchange.com/questions/40311/how-many-kdf-rounds-for-an-ssh-key#:~:text=Since%20the%20cipher%20is%20rekeyed%2064%20times%20per,choosing%20the%20number%20of%20KDF%20rounds%20here.%20Share)
* [](https://goteleport.com/blog/comparing-ssh-keys/)
* [](https://goteleport.com/blog/ssh-handshake-explained/)
* [](https://goteleport.com/blog/ssh-key-management/)
* [](https://goteleport.com/blog/how-to-ssh-properly/)

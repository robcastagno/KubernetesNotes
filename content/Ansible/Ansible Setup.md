1. Install Ansible:
```
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
```
2. Create Inventory:
```
sudo nano /etc/ansible/hosts

[<network>]
<IP1>
<IP2>
<IP3>
```
3. Verify Hosts
`ansible all --list-hosts`
4. Set up SSH connections:
```
add public ssh keys to authorized keys
ssh-keygen -t ed25519
>setup process
ssh-copy-id -i ~/.ssh/id_rsa.pub <user>@<hostname>

test ssh connection
ssh <user>@<host>
```
5.  Ping the managed nodes:
`ansible <all/groupname> -m ping`
```
ssh-agent bash/zsh
ssh-add <sshkey>
```

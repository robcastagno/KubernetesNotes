# Setup
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
# Inventories
Create a file `inventory.yaml`
```
<group>:
  hosts:
    <hostname1>:
      ansible_host: <domain1>
    <hostname2>:
      ansible_host: <domain2>
    <hostname3>:
      ansible_host: <domain3>
```
Verify the inventory:
`ansible-inventory -i inventory.yaml --list`
Ping the inventory:
`ansible <group> -m ping -i inventory.yaml`

Inventory structure:

## Metagroups

After creating groups, they can be organized into metagroups.
```
<metagroup>:
  children:
    <group1>:
    <group2>:
```

## Variables

Variables allow for changing and tweaking values of managed nodes so they don't need to be passed by ansible commands.
They can apply to specific hosts or all hosts in a group.

```
<group>:
  hosts:
    <host1>:
      ansible_host: <domain1>
      http_port: 80
    <host2>:
      ansible_host: <domain2>
      http_port: 443
```

```
<group>:
  hosts:
    <host1>:
      ansible_host: <domain1>
      http_port: 80
    <host2>:
      ansible_host: <domain2>
      http_port: 443
  vars:
    ansible_user: my_server_user
```
# Playbooks
Create a playbook yaml file:
`playbook.yaml`
Run the playbook:
`ansible-playbook -i inventory.yaml playbook.yaml`
Check the playbook:
`ansible-playbook --check playbook.yaml`
`--check, --diff, --list-hosts, --list-tasks, --syntax-check`

`--ask-become-pass for root password`
## Syntax
```
Example:
---
- name: Update web servers
  hosts: webservers
  remote_user: root

  tasks:
  - name: Ensure apache is at the latest version
    ansible.builtin.yum:
      name: httpd
      state: latest

  - name: Write the apache config file
    ansible.builtin.template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf

- name: Update db servers
  hosts: databases
  remote_user: root

  tasks:
  - name: Ensure postgresql is at the latest version
    ansible.builtin.yum:
      name: postgresql
      state: latest

  - name: Ensure that postgresql is started
    ansible.builtin.service:
      name: postgresql
      state: started
```
# Vaults
1. Create a `group_vars/` subdirectory named after the group.

2. Inside this subdirectory, create two files named `vars` and `vault`.

3. In the `vars` file, define all of the variables needed, including any sensitive ones.

4. Copy all of the sensitive variables over to the `vault` file and prefix these variables with `vault_`.

5. Adjust the variables in the `vars` file to point to the matching `vault_` variables using jinja2 syntax: `db_password: {{ vault_db_password }}`.

6. Encrypt the `vault` file to protect its contents.
`ansible-vault create --vault-id`
7. Use the variable name from the `vars` file in your playbooks.
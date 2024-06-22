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


# Creating Terminal Profiles
1. Open up settings
2. Add a new profile
3. Configure it and save.
4. Click Open JSON and tweak the profile to contain the following:
```
{
	"name": "<name>",
	"tabTitle": "<Tab Title>",
	"commandline": "ssh <Profile>",
	"icon": "<emoji or file>"
},
```
[nerdfonts.com] for nerd fonts. These are necessary for Starship

# Configuring the Windows SSH Client
1. Generate ED25519 Key and upload it to the remote server:
```
(local machine)
ssh-keygen -t ed25519 -o
Get-Content \path\to\<key-name>.pub | ssh user@remote_server "cat >> .ssh/authorized_keys"
<type user password>
```
2. Add a new host in `C:\Users\<username>/.ssh/config`:
```
Host <hostname>
  HostName <IP Address>
  Port <Port>
  User <Username>
  IdentityFile <Private Key File Location>
```
3. Install OpenSSH Client and Server:
```
Get-WindowsCapability -Online | Where-Object Name -like ‘OpenSSH*’
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```
If an error, run:`Add-WindowsCapability -Online -Name "Msix.PackagingTool.Driver~~~~0.0.1.0`
4. Enable the OpenSSH Authentication Agent
```
Go to Services > OpenSSH Authentication Agent
Right click > Properties
Set Startup Type to Automatic
Start the Service
```
5. Add the key to the authentication agent.
`ssh-add /path/to/<key-name>`
This might be less secure than requiring the password to use the key each time, but this is why 2FA is also required.


**Setting up SSH in Windows Terminal/Powershell**

Generate ED25519 Key:
```
(local machine)
ssh-keygen -t ed25519
Get-Content \path\to\id_ed25519.pub | ssh user@remote_server "cat >> .ssh/authorized_keys"
<type user password>
```
`C:\Users\<username>/.ssh/config` setup:
```
Host <hostname>
  HostName <IP Address>
  Port <Port>
  User <Username>
  IdentityFile <ed25519 File Location>
```

Install OpenSSH Client and Server:
```
Get-WindowsCapability -Online | Where-Object Name -like ‘OpenSSH*’
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```
If an error, run:`Add-WindowsCapability -Online -Name "Msix.PackagingTool.Driver~~~~0.0.1.0`
```
Go to Services > OpenSSH Authentication Agent
Right click > Properties
Set Startup Type to Automatic
Start the Service
```
`ssh-add \path\to\id_ed25519`
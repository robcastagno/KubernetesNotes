**Initial Setup and hardening of Ubuntu Server 20.04:**
```
sudo timedatectl set-timezone America/New_York
sudo apt update && sudo apt upgrade -y
sudo swapoff -a
sudo apt install -y libpam-google-authenticator fail2ban curl apt-transport-https git wget gnupg2 software-properties-common lsb-release ca-certificates uidmap
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```
Configure Google Authenticator:
```
google-authenticator
y
<scan QR Code and enter response code>
<Grab emergency codes>
y
y
n
y
```
Disable Root User:
`sudo nano /etc/shadow`
```
<Set password to ! to disable an account>
```
Configure OpenSSH Server:
`sudo nano /etc/ssh/sshd_config`
```
Port 22222
Protocol 2
AllowUsers <username>
AuthenticationMethods publickey,keyboard-interactive
ChallengeResponseAuthentication yes

KbdInteractiveAuthentication yes

LoginGraceTime 5m
PermitRootLogin no
MaxAuthTries 5
PubkeyAuthentication yes
PasswordAuthentication no
PermitEmptyPasswords no
KerberosAuthentication no
GSSAPIAuthentication no

Match Address <IP range>/<subnet> User <username>
      AuthenticationMethods publickey
      KbdInteractiveAuthentication no

Match User <username>@*
      AuthenticationMethods publickey,keyboard-interactive
      KbdInteractiveAuthentication yes

#Match User <New account>
#      UsePAMSubsystem yes
#      AuthenticationMethods password
#      PasswordAuthentication yes
```
Configure PAM: 
`sudo nano /etc/pam.d/sshd`
```
#@include common-auth
...
auth required pam_google_authenticator.so
```
Configure PAM for new users:
`sudo nano /etc/pam.d/sshd-newusers`
```
#%PAM-1.0
auth [success=1 default=ignore] pam_succeed_if.so uid >= 1000 quiet
auth required pam_google_authenticator.so
auth [success=1 default=ignore] pam_unix.so nullok_secure
auth requisite pam_deny.so
auth required pam_permit.so
```
Configure Fail2Ban:
`sudo nano /etc/fail2ban/jail.local`
```
<change sshd port>
```
Disable Swapspace:
`sudo nano /etc/fstab`
```
<comment out swap line>
```
Restart everything so changes take effect:
```
sudo reboot
```
or
```
sudo systemctl restart sshd
```


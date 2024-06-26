# 1- General Ubuntu Setup
Server setup process: Ubuntu 20.04:
```
English
Update to new Installer
Done (Keyboard)
Done (type of Install)
Done (Internet)
Done (Proxy)
Done (Mirror)
Custom storage layout (Done)
```
Setup for storage on current system:
```
rule of thumb: Use Btrfs for data that needs preservation, xfs for partitions that aren't set sizes, ext4 for its journaling
local: remaining xfs /

Done (Storage Configuration)
```
Setup for user configuration:
```
Your Name: Rob Castagno
Your server's name: <hostname>
Pick a username <username>
Choose a password: <pass>
Confirm your password: <pass>

Done (User config)
```
Final bit of setup: 
```
Install OpenSSH server, import github ssh keys
Done (SSH Setup)

Done (Skip snaps)
Reboot Now (remove install drive)
```

# 2- Server Hardening
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


# 3- Starting the nfs Server
Install nfs server:
```
sudo apt install nfs-kernel-server -y
```

Configure the nfs server:
`sudo nano /etc/exports`
```
/storage  <KUBERNETES_NODE_IP>(rw,sync,no_root_squash,no_subtree_check)
```

# 4- Configuring zsh
Install Zsh:
`sudo apt install zsh -y`

Go through setup process: `zsh`
My Config:
```
# The following lines were added by compinstall
zstyle ':completion:*' completer _complete _ignored _correct
zstyle ':completion:*' format 'Completing %d'
zstyle ':completion:*' matcher-list '' 'm:{[:lower:]}={[:upper:]} m:{[:lower:][:upper:]}={[:upper:][:lower:]}' 'r:|[._-]=* r:|=*' 'l:|=* r:|=*'
zstyle ':completion:*' max-errors 2 numeric
zstyle :compinstall filename '/home/pcb298/.zshrc'

autoload -Uz compinit
compinit
# End of lines added by compinstall
# Lines configured by zsh-newuser-install
HISTFILE=~/.histfile
HISTSIZE=9001
SAVEHIST=9001
setopt autocd
bindkey -e
# End of lines configured by zsh-newuser-install
```
Change shell for root and then for specific user:
```
sudo -s
chsh -s /bin/zsh root
chsh -s /bin/zsh <user>
```

Install Oh My Zsh:
`sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`
Install Zsh autosuggestions:
`git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions`
Install zsh-syntax-highlighting:
`git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting`

Update plugins list:
`nano .zshrc`
```
plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
    )
```
# 5- Setting up Starship
install starship:
`sh -c "$(curl -fsSL https://starship.rs/install.sh)"`
Add Starship to the bash config:
`nano ~/.zshrc`
`eval "$(starship init zsh)"`
Reload the shell
`source ~/.zshrc`
Create the starship config:
`mkdir ~/.config && touch ~/.config/starship.toml`
Edit the config: `nano ~/.config/starship.toml`
(Use configs provided in [Setting up Windows SSH](obsidian://open?vault=Documentation%20Portfolio&file=Starship%20Configuration)

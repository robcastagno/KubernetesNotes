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

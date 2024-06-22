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
(Use configs provided in [Setting up Windows SSH](obsidian://open?vault=Documentation%20Portfolio&file=Kubernetes%20Cluster%20Setup%2FWindows%20Stuff%2FWindows%20Starship%20Setup)

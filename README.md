# Custom Shell Setup

---
### (WIN) WSL
* Download and install the WSL (Ubuntu is good) following the instructions [here](https://msdn.microsoft.com/en-au/commandline/wsl/install_guide)
---

### Hyper
* Download for the relevant OS [here](https://hyper.is)
* Open the Preferences file (`Ctrl` + `,` from the hyper terminal)
  * Set the `shell` setting to the relevant path below to make it automatically open with Bash:
    * (WIN) `C:\\Windows\\System32\\bash.exe`
    * (UBT) `/bin/bash` (should be unnecessary as will use login shell by default)
  * Set the `shellArgs` setting to `['~']` to make home the default opening directory
  * Set any style settings you'd like (I set `fontSize` to 14)
* Save the file to save settings

### Zsh
* Run `sudo apt-get install zsh`
* Open `~/.bashrc` in an editor and add the following to the bottom:
```
# use zsh as default.
if [ -t 1 ]; then
    exec zsh
fi
```
* Open `/root/.bashrc` in an editor (need to use sudo privileges) and add the following to the bottom, so the root user also uses Zsh automatically (do the same for `/etc/skel/.bashrc` so the same applies to any newly created users):
```
# use zsh as default.
if [ ! -z "$PS1" ]; then
    exec /bin/zsh $*
fi
```

### Oh My Zsh
* Log in as root user `sudo -i` (going to install globally for all users)
* Install in the root user's home directory `/root/`
```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
  * Read docs [here](https://github.com/robbyrussell/oh-my-zsh)
* Move the Oh My Zsh folder to the user shared directory
```
mv /root/.oh-my-zsh /usr/share/oh-my-zsh
```
* Move the .zshrc filel to that directory
```
mv /root/.zshrc /usr/share/zshrc
```
* Open `/usr/share/zshrc` in an editor and change the `export ZSH="/root/.oh-my-zsh"` line to `export ZSH="/usr/share/oh-my-zsh"`
* Create a custom theme
  * Create a new file `/usr/share/oh-my-zsh/themes/mytheme.zsh-theme` (can choose a different name to 'mytheme') with the following in it (a slight variation on the robbyrussell theme that includes the user if not logged in as the default user - see a few bullets down - and also the host if connected through ssh):
```
local ret_status="%(?:%{$fg_bold[green]%}➜ :%{$fg_bold[red]%}➜ )%{$reset_color%}"
PROMPT='$(prompt_context)${ret_status} %{$fg[cyan]%}%c %{$reset_color%}$(git_prompt_info)'

ZSH_THEME_GIT_PROMPT_PREFIX="%{$fg_bold[blue]%}git:(%{$fg[red]%}"
ZSH_THEME_GIT_PROMPT_SUFFIX=" %{$reset_color%}"
ZSH_THEME_GIT_PROMPT_DIRTY="%{$fg[blue]%}) %{$fg[yellow]%}✗"
ZSH_THEME_GIT_PROMPT_CLEAN="%{$fg[blue]%})"

# Context: user@hostname (who am I and where am I)
prompt_context() {
  if [[ -n "$SSH_CLIENT" ]]; then
    echo -n "%(!.%{%F{yellow}%}.)$USER@%mi %{$reset_color%}"
  elif [[ "$USER" != "$DEFAULT_USER" ]]; then
    echo -n "%(!.%{%F{yellow}%}.)$USER %{$reset_color%}"
  fi
}
```
  * Open `/usr/share/zshrc` in an editor and change the `ZSH_THEME="robbyrussell"` line to `ZSH_THEME="mytheme"` (or whatever you named your theme)
* Create a hard link to the zshrc file to ensure new users get a copy of it
```
ln /usr/share/oh-my-zsh/zshrc /etc/skel/.zshrc
```
* Copy the zshrc file to root and any existing users' home directories (this will need to be done whenever it's updated)
```
cp /usr/share/oh-my-zsh/zshrc /root/.zshrc
```
```
cp /usr/share/oh-my-zsh/zshrc /home/<user>
```
* Create the default user environment variable system wide
  * Create a new file `/etc/profile.d/env-vars.sh` with the following in it (with your default user):
```
export DEFAULT_USER="<user>"
```
* Close the terminal and open a new one for changes to take effect

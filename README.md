# Custom Shell Setup

---
### (WIN) WSL
* Download and install the WSL (Ubuntu is good) following the instructions [here](https://msdn.microsoft.com/en-au/commandline/wsl/install_guide)
---

### Hyper
* Download for the relevant OS [here](https://hyper.is)
* Open the Preferences file (`Ctrl` + `,` from the hyper terminal)
  * Set the `shell` setting to the relevant path below to make it automatically open with Bash:
    * (WIN) `C:\\Windows\\System32\\wsl.exe`
    * (UBT) (should be unnecessary as will use login shell by default)
  * Set the `shellArgs` setting to `['~']` to make home the default opening directory
  * Set any style settings you'd like (I set `fontSize` to 14)
* Save the file to save settings

### Zsh
* Run `sudo apt-get install zsh`
* Run `chsh -s /bin/zsh` to change your default login shell to zsh
* Open `/etc/zsh/zprofile` in an editor (using sudo privileges) and add the following to the bottom:
```
. /etc/profile
```

### Oh My Zsh
* Log in as root user `sudo -i` (going to install globally for all users)
* Install in the root user's home directory `/root/`
```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
  * Read docs [here](https://github.com/robbyrussell/oh-my-zsh)
* Move the oh-my-zsh folder to the user shared directory
```
mv /root/.oh-my-zsh /usr/share/oh-my-zsh
```
* Move the zshrc file to that directory
```
mv /root/.zshrc /usr/share/oh-my-zsh/zshrc
```
* Open `/usr/share/oh-my-zsh/zshrc` in an editor and change the `export ZSH="/root/.oh-my-zsh"` line to `export ZSH="/usr/share/oh-my-zsh"`
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
    echo -n "%(!.%{%{$fg[yellow]%}%}.)$USER@%mi %{$reset_color%}"
  elif [[ "$USER" != "$DEFAULT_USER" ]]; then
    echo -n "%(!.%{%{$fg[yellow]%}%}.)$USER %{$reset_color%}"
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
cp /usr/share/oh-my-zsh/zshrc /home/<user>/.zshrc
```
* Create the default user environment variable system wide
  * Create a new file `/etc/profile.d/env-vars.sh` with the following in it (with your default user):
```
export DEFAULT_USER="<user>"
```
* Close the terminal and open a new one for changes to take effect

### Zsh Plugins

* Log in as root user `sudo -i`
* Install zsh-syntax-highlighting and zsh-autosuggestions
  * Clone the repositories for each in the `/usr/share/oh-my-zsh/plugins` directory
```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
```
```
git clone https://github.com/zsh-users/zsh-autosuggestions.git
```
  * See docs for zsh-syntax-highlighting [here](https://github.com/zsh-users/zsh-syntax-highlighting) and zsh-autosuggestions [here](https://github.com/zsh-users/zsh-autosuggestions)
* Activate these plugins and also the colored-man-pages plugin which comes with the oh-my-zsh installation
  * Open `/usr/share/oh-my-zsh/zshrc` in an editor and add `z`, `colored-man-pages`, `zsh-syntax-highlighting` and `zsh-autosuggestions` to the `plugins` array, so it looks like:
```
plugins=(
  git
  z
  colored-man-pages
  zsh-syntax-highlighting
  zsh-autosuggestions
)
```
* Copy the zshrc file to root and any existing users' home directories (this will need to be done whenever it's updated)
```
cp /usr/share/oh-my-zsh/zshrc /root/.zshrc
```
```
cp /usr/share/oh-my-zsh/zshrc /home/<user>/.zshrc
```
* Close the terminal and open a new one for changes to take effect (or `source` the relevant zshrc file for each user)

---

### TODO: Hyper Plugins
* Hyper plugins don't seem to work currently with either Windows, WSL, Zsh or some combination of those, check back another time
* The following plugins look interesting: hyperline, hyper-statusline, hypercwd, hyperlinks, hyper-search, hyper-copycat, hyperterm-tabs, hyper-pane

# Custom Shell Setup

---

### [WINDOWS] WSL
* Download and install the WSL (Ubuntu is good) following the instructions [here](https://msdn.microsoft.com/en-au/commandline/wsl/install_guide)

---

### (OPTIONAL) Hyper
* Download for the relevant OS [here](https://hyper.is)
* Open the Preferences file (`Ctrl` + `,` from the hyper terminal)
  * Set the `shell` setting to the relevant path below to make it automatically open with Bash:
    * [WINDOWS] `C:\\Windows\\System32\\wsl.exe`
    * [UBUNTU/MAC] (should be unnecessary as will use login shell by default)
  * Set the `shellArgs` setting to `['~']` to make home the default opening directory
  * Set any style settings you'd like (I set `fontSize` to 14)
* Save the file to save settings

---

### [WINDOWS-WSL/UBUNTU] Zsh
* Run `sudo apt-get install zsh`
* Run `chsh -s /bin/zsh` to change your default login shell to zsh
* Open `/etc/zsh/zprofile` in an editor (using sudo privileges) and add the following to the bottom:
```
. /etc/profile
```

---

### Oh My Zsh
* Install in user home directory `~`
  * Read docs [here](https://github.com/robbyrussell/oh-my-zsh)
```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
* (OPTIONAL) Open `~/.zshrc` in an editor and add the following line to the bottom so the physical path is used when following symbolic links (VSCode on WSL, for example, gets confused otherwise when debugging)
```
alias cd="cd -P"
```
* Create a custom theme
  * Create a new file `~/.oh-my-zsh/themes/mytheme.zsh-theme` (can choose a different name to 'mytheme') with the following in it (a slight variation on the robbyrussell theme that includes the user if not logged in as the default user - see a few bullets down - and also the host if connected through ssh):
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
  else
    echo -n "%(!.%{%{$fg[yellow]%}%}.)$USER %{$reset_color%}"
  fi
}
```
* Open `~/.zshrc` in an editor and change the `ZSH_THEME="robbyrussell"` line to `ZSH_THEME="mytheme"` (or whatever name was given to the theme)
* Close the terminal and open a new one for changes to take effect

### Zsh Plugins
* Install zsh-syntax-highlighting and zsh-autosuggestions
  * See docs for zsh-syntax-highlighting [here](https://github.com/zsh-users/zsh-syntax-highlighting) and zsh-autosuggestions [here](https://github.com/zsh-users/zsh-autosuggestions)
  * Clone the repositories for each in the `~/.oh-my-zsh/plugins` directory
```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
```
```
git clone https://github.com/zsh-users/zsh-autosuggestions.git
```
* Activate these plugins and also the colored-man-pages plugin which comes with the oh-my-zsh installation
  * Open `~/.zshrc` in an editor and add `z`, `colored-man-pages`, `zsh-syntax-highlighting` and `zsh-autosuggestions` to the `plugins` array:
```
plugins=(
  git
  z
  colored-man-pages
  zsh-syntax-highlighting
  zsh-autosuggestions
)
```
* Close the terminal and open a new one for changes to take effect (or `source` the relevant zshrc file for each user)

---

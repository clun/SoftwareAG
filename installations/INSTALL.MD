# Install a demo server

- Windows Update

- Install GitBash from [HERE](https://git-for-windows.github.io/)

- Sample Profile (C:/Users/${username})
```shell
# ------------------------------------ #
# Proposition de script shell          #
#                                      #
# Author        : clunven              #
# Last Modified : 2016_03_01           #
# ------------------------------------ #

# ------------------------------------ #
# (1) Variables Environnements
# ------------------------------------ #

# Surcharge pour le prompt
export PS1="\[\033[31m\]\u\[\033[00m\]@\[\033[32m\]\h\[\033[00m\]:\[\033[34m\]\w\[\033[00m\]> "

# Couleurs dans le shell
export CLICOLOR=1
export LSCOLORS=dxfxcxdxbxegedabagacad

# Navigation
alias cd..='cd ../'
alias ..='cd ../'
alias ...='cd ../../'
alias .3='cd ../../../'
alias .4='cd ../../../../'
alias .5='cd ../../../../../'
alias .6='cd ../../../../../../'

# Clear terminal display
alias c='clear'

# Chemin
alias path='echo -e ${PATH//:/\\n}'
export PATH=$PATH;$M2_HOME/bin

# GIT
alias gita="git add -A"
alias gitp="git push origin master"
alias gitu="git pull"
alias sag="cd C:/SoftwareAG"

# Affiche contenu repertoire
cd() {
  builtin cd "$@"; ll;
}

# Creation rep et on rentre dedans
mcd () {
  mkdir -p "$1" && cd "$1";
}


# git commit
gitm () {
        git commit -m "$@" ;
}

clear
echo "-------------------------------------------"
printf "Bienvenue ${blue}`whoami`${reset} sur ${red}`hostname`${reset}\n"
printf "Nous sommes le ${blue}`date +%x`${reset} il est ${blue}`date +%X`${reset}\n"
echo "-------------------------------------------"
```

- Install chocolatey
```
@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```
- Install notepad++
```
choco install notepadplusplus
```



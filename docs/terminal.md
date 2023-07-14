## Terminal setup for a new machine

#### Vim: `~/.vimrc` file

```bash
"syntax
syntax on

"General useful settings
set pastetoggle=<F2>
set modeline
set modelines=5

set guifont=Menlo\ Regular:h14
set ruler
set showmode
set wrap
set ignorecase
set smartcase

"Python settings
set nowrap
set tabstop=8
set shiftwidth=4
set softtabstop=4
set expandtab
```

#### Bash: `~/.bash_profile` file

```bash
# .bash_profile
export VISUAL=/usr/bin/vi
export CLICOLOR=1
export PS1="\h:\w>$ "


# bash completion
if [ -f ~/.bash_completion ]; then
    . ~/.bash_completion
fi


# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/Users/yunjunz/tools/mambaforge/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/Users/yunjunz/tools/mambaforge/etc/profile.d/conda.sh" ]; then
        . "/Users/yunjunz/tools/mambaforge/etc/profile.d/conda.sh"
    else
        export PATH="/Users/yunjunz/tools/mambaforge/bin:$PATH"
    fi
fi
unset __conda_setup

if [ -f "/Users/yunjunz/tools/mambaforge/etc/profile.d/mamba.sh" ]; then
    . "/Users/yunjunz/tools/mambaforge/etc/profile.d/mamba.sh"
fi
# <<< conda initialize <<<


# conda environments
if [ -z ${PYTHONPATH+x} ]; then export PYTHONPATH=""; fi
# private dev tools
export PYTHONPATH=${PYTHONPATH}:~/tools/dev
export PATH=${PATH}:~/tools/dev/tools
# manually installed packages: nodejs and npm (for jupyter-lab) and msrsync
export PATH=${PATH}:/usr/local/bin:~/tools/bin

# env aliases
alias load_insar='conda activate insar; source ~/tools/conda_envs/insar/config.rc'
alias load_isce2_dev='export ISCE_VERSION="_dev"; conda activate isce2; source ~/tools/conda_envs/isce2/config.rc'
alias load_fringe='conda activate fringe; source ~/tools/conda_envs/fringe/config.rc'
alias load_opera='conda activate opera; source ~/tools/conda_envs/opera/config.rc'

# useful aliases and functions
alias sou='source ~/.bash_profile; echo "sourceing ~/.bash_profile"'
alias ls='ls -GFh'
alias ll='ls -GFhal'
alias m='more'
alias rrsync='rsync -avzh --progress'
alias rm=~/tools/utils/shell-safe-rm/bin/rm.sh
alias nvidia='watch -n 1 nvidia-smi'
function ff() { find . -name \*"$@"\* -print; }
function igrep() { grep -rn --color --include=*.{py,ipynb,sh,txt,cfg,xml,rst,md,yml,yaml,html,m,cpp,h} \
    --exclude=*{checkpoint.,.egg-info}* "$@" .; }
alias julab='jupyter-lab'
alias jubook='jupyter-notebook'
alias cona='conda activate'
alias cond='conda deactivate'


# miscellaneous
export PYTHONPATH=${PYTHONPATH}:~/tools/utils/geoidheight
export JAVA_HOME=/Library/Java/JavaVirtualMachines/openjdk11/Contents/Home
```

#### Bash: `~/.bashrc` file [optional]

```bash
# .bashrc

# source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi

source ~/.bash_profile
```

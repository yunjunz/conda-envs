```bash
# .bash_profile
export VISUAL=/usr/bin/vi
export CLICOLOR=1
export PS1="\h:\w>$ "


use_port=0  # 0 for conda, 1 for port

if [ $use_port -eq 1 ]; then
    # >>> MacPorts >>>
    if [ -z ${MANPATH+x} ]; then export MANPATH=""; fi
    export GDAL_DRIVER_PATH=/opt/local/lib/gdalplugins
    #For py37-pyhdf in macports
    export INCLUDE_DIRS=/opt/local/include
    export LIBRARY_DIRS=/opt/local/lib
    # MacPorts Installer addition on 2017-09-02_at_01:27:12: adding an appropriate PATH variable for use with MacPorts.
    export PATH=/opt/local/bin:/opt/local/sbin:${PATH}
    export MANPATH=/opt/local/share/man:${MANPATH}
    # Finished adapting your PATH environment variable for use with MacPorts.
    # <<< MacPorts <<<

else
    # >>> conda initialize >>>
    # !! Contents within this block are managed by 'conda init' !!
    __conda_setup="$('/Users/yunjunz/tools/miniconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
    if [ $? -eq 0 ]; then
        eval "$__conda_setup"
    else
        if [ -f "/Users/yunjunz/tools/miniconda3/etc/profile.d/conda.sh" ]; then
            . "/Users/yunjunz/tools/miniconda3/etc/profile.d/conda.sh"
        else
            export PATH="/Users/yunjunz/tools/miniconda3/bin:$PATH"
        fi
    fi
    unset __conda_setup
    # <<< conda initialize <<<
fi


# User specific aliases and functions
alias sou='source ~/.bash_profile; echo "sourceing ~/.bash_profile"'
alias ls='ls -GFh'
alias ll='ls -GFhal'
alias m='more'
alias rrsync='rsync -avzh --progress'
alias rm=~/tools/utils/shell-safe-rm/bin/rm.sh
function ff() { find . -name \*"$@"\* -print; }
function igrep() { grep -rn --color --include=*.{py,sh,txt,cfg,m,cpp,h} "$@" .; }
function igrepy() { grep -rn --color --include=*.{ipynb,py,sh,txt,cfg,m,cpp,h} "$@" .; }
alias julab='jupyter-lab'
alias jubook='jupyter-notebook'


# Environments
export PATH=${PATH}:/usr/local/bin  #for manually installed nodejs and npm for jupyter-lab
# insar: mintpy, pyaps, aria-tools and isce2
alias load_insar='conda activate insar; source ~/tools/conda_envs/insar/config.rc; rm -f isce.log'
# isce_gpu
alias load_isce_gpu='conda activate isce_gpu; source ~/tools/conda_envs/isce_gpu/config.rc'
# fringe
alias load_fringe='conda activate fringe; source ~/tools/conda_envs/fringe/config.rc'
```

+ `.bashrc` file:

```bash
# .bashrc

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi


source ~/.bash_profile

```

+ `.bash_profile` file:

```bash
# .bash_profile
export VISUAL=/usr/bin/vi
export CLICOLOR=1
export PS1="\h:\w>$ "


# Python environment
python_env='conda'  # port / conda

if [ $python_env == 'port' ]; then
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
        # not recommended anymore in conda 4.4+
        #else
        #    export PATH="/Users/yunjunz/tools/miniconda3/bin:$PATH"
        fi
    fi
    unset __conda_setup
    # <<< conda initialize <<<
fi


# Conda environments
if [ -z ${PYTHONPATH+x} ]; then export PYTHONPATH=""; fi
export PATH=${PATH}:/usr/local/bin           #for manually installed nodejs and npm for jupyter-lab
# set isce2/3 installation method for insar/opera environment
export ISCE_INSTALL_METHOD='conda'           # conda / source
# insar: mintpy, aria-tools and isce2
alias load_insar='conda activate insar; source ~/tools/conda_envs/insar/config.rc'
alias load_atbd='conda activate atbd; source ~/tools/ATBD/docs/config.rc'
# isce_dev: the latest ISCE-2 version on GitHub
alias load_isce_dev='export ISCE_VERSION="_dev"; conda activate isce2; source ~/tools/conda_envs/isce2/config.rc'
# fringe
alias load_fringe='conda activate fringe; source ~/tools/conda_envs/fringe/config.rc'
# opera
alias load_opera='conda activate opera; source ~/tools/conda_envs/opera/config.rc'

# Useful aliases and functions
alias sou='source ~/.bash_profile; echo "sourceing ~/.bash_profile"'
alias ls='ls -GFh'
alias ll='ls -GFhal'
alias m='more'
alias rrsync='rsync -avzh --progress'
alias rm=~/tools/utils/shell-safe-rm/bin/rm.sh
function ff() { find . -name \*"$@"\* -print; }
function igrep() { grep -rn --color --include=*.{py,ipynb,sh,txt,cfg,xml,rst,md,yml,yaml,html,m,cpp,h} --exclude=*{checkpoint.,.egg-info}* "$@" .; }
alias julab='jupyter-lab'
alias jubook='jupyter-notebook'
alias cona='conda activate'
alias cond='conda deactivate'


# Miscellaneous
export PYTHONPATH=${PYTHONPATH}:~/tools/utils/geoidheight
export JAVA_HOME=/Library/Java/JavaVirtualMachines/openjdk11/Contents/Home

```

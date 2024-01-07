## Install GAMMA software on Linux

### 1. [Install conda](../README.md#1-install-conda)

### 2. Install GAMMA to `gamma` environment

#### a. Download source code

```bash
cd ~/tools
git clone git@github.com:yunjunz/conda-envs.git

mkdir -p gamma; cd gamma
tar -xzvf GAMMA_SOFTWARE.linux64_ubuntu1804.tar.gz
```

Edit `~/tools/conda-envs/gamma/config.rc` file to adjust the path for `GAMMA_HOME`.

#### b. Create `gamma` environment and install dependencies

```bash
conda create --name gamma
conda activate gamma

mamba install --yes --file ~/tools/conda-envs/gamma/requirements.txt

# install dependencies not available from conda
# install gimp via apt if not found via "command -v <the_command>"
if ! command -v gimp &> /dev/null; 
then 
    echo "run apt to install gimp."
    sudo apt install gimp
    exit
fi

# fix the weird dependency of gamma on libgdal.so.20
# gamma seems hardwired libgdal version 20
source ~/tools/conda-envs/gamma/config.rc
ln -s ${CONDA_PREFIX}/lib/libgdal.so ${GAMMA_HOME}/lib/libgdal.so.20
```

#### c. Setup

Create an alias `load_gamma` in `~/.bash_profile` file for easy activation, e.g.:

```bash
alias load_insar='conda activate gamma; source ~/tools/conda-envs/gamma/config.rc'
```

#### d. Test the installation

```bash
load_gamma
atm_mod
disSLC
mb_pt
adf
cc_ad
```

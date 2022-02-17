## Install [FRInGE](https://github.com/isce-framework/fringe.git)

### 1. [Install conda](../README.md#1-install-conda)

### 2. Install FRInGE to `fringe` environment

#### a. Download source code

```bash
cd ~/tools
mkdir FRInGE; cd FRInGE
mkdir install build src; cd src
git clone https://github.com/isce-framework/fringe.git
```

#### b. Install dependencies to `fringe` environment

You might need to modify the install directory in **conda_envs/fringe/config.rc** file.

```bash
# create new environment
conda create --name fringe --yes
conda activate fringe

# install pre-requisites
mamba install --yes --file ~/tools/conda_envs/fringe/requirements.txt
```

#### c. Build and install FRInGE from source

```bash
# install from source code
source ~/tools/conda_envs/fringe/config.rc

cd ~/tools/FRInGE/build
cmake -DCMAKE_INSTALL_PREFIX=../install ../src/fringe -DBLA_VENDOR=Generic
make -j 8 all
make install
```

#### d. Setup

Create an alias `load_fringe` in `~/.bash_profile` file for easy activation, e.g.:

```bash
alias load_insar='conda activate fringe; source ~/tools/conda_envs/fringe/config.rc'
```

#### e. Test the installation

```bash
load_fringe
sequential.py -h
```

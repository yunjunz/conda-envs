## Install the development environment for OPERA project

### 1. [Install conda](../README.md#1-install-conda)

Edit `~/.condarc` file to add `conda-forge` and `avalentino` channels as:

```
channels:
  - conda-forge
  - avalentino
  - defaults
```

### 2. Install `opera-adt` packages

#### a. Download source code

```bash
cd ~/tools
git clone https://github.com/opera-adt/COMPASS.git
git clone https://github.com/opera-adt/sentinel1-reader.git
git clone https://github.com/opera-adt/sentinel1-burst-id.git
git clone https://gitlab.com/s1-etad/s1-etad.git
```

#### b. Install dependencies to `opera` enrivonment

```bash
# create a new conda conda environment
conda create --name opera --yes
conda activate opera

# install dependencies
cd ~/tools
mamba install --file conda_envs/opera/requirements.txt --file sentinel1-reader/requirements.txt --file sentinel1-burst-id/requirements.txt
```

#### c. Setup

Create an alias in `~/.bash_profile` file for easy activation.

```bash
alias load_opera='conda activate opera; source ~/tools/conda_envs/opera/config.rc'
```


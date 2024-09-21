## Code installation for the 2024 one-day tutorial on InSAR time series analysis with MintPy

### 0. (For Windows only) [Install Windows Subsystem for Linux](../docs/wsl.md)

### 1. Install conda

```bash
mkdir -p ~/tools; cd ~/tools
# for macOS, use Mambaforge-MacOSX-x86_64.sh, and optionally use `curl -L -O https://...` syntax to download
wget https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-Linux-x86_64.sh
bash Mambaforge-Linux-x86_64.sh -b -p ~/tools/mambaforge
~/tools/mambaforge/bin/mamba init bash
```

Close and restart the shell for changes to take effect. Then install the following utilities:

```bash
mamba install wget git tree --yes
```

### 2. Install ISCE-2 and MintPy to the `earthscope` environment

#### a. Download source code

```bash
cd ~/tools
git clone https://github.com/yunjunz/conda-envs.git
git clone https://github.com/insarlab/MintPy.git
git clone https://github.com/insarlab/MintPy-tutorial.git utils/MintPy-tutorial
git clone https://github.com/isce-framework/isce2.git isce2/src/isce2
```

#### b. Install softwares

```bash
# create new environment
mamba create --name earthscope --yes
mamba activate earthscope

# install dependencies
cd ~/tools
mamba install --file conda-envs/earthscope/requirements.txt

# install dependencies not available on conda-forge
python -m pip install jupyter_nbextensions_configurator

# install the development version of mintpy
python -m pip install -e MintPy
```

#### c. Setup

Create an alias `load_earthscope` in `~/.bash_profile` file for easy activation, _e.g._:

```bash
alias load_earthscope='conda activate earthscope; source ~/tools/conda-envs/earthscope/config.rc'
```

#### d. Test the installation

```bash
load_earthscope            # wram up the conda environment
topsApp.py -h              # test ISCE-2
smallbaselineApp.py -h     # test MintPy
```

### 3. Start up the notebook environment

```bash
load_earthscope            # warm up the conda environment
jupyter-lab                # start up the jupyter notebook
```

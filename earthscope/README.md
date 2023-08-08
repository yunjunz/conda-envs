## Code installation for the [2023 EarthScope short course (ISCE+)](https://www.earthscope.org/event/2023-insar-isce-short-course/)

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

### 2. Install ISCE-2, ARIA-tools and MintPy to the `earthscope` environment

#### a. Download source code

```bash
cd ~/tools
git clone https://github.com/isce-framework/isce2.git isce2/src/isce2
git clone https://github.com/aria-tools/ARIA-tools.git
git clone https://www.unavco.org/gitlab/unavco_public/ssara_client.git utils/SSARA
git clone https://github.com/yunjunz/conda_envs.git
```

#### b. Install dependencies

```bash
# create new environment
mamba create --name earthscope --yes
mamba activate earthscope

# install dependencies
cd ~/tools
mamba install --file conda_envs/earthscope/requirements.txt --file ARIA-tools/requirements.txt --yes

# install dependencies not available on conda-forge
ln -s ${CONDA_PREFIX}/bin/cython ${CONDA_PREFIX}/bin/cython3
python -m pip install ipynb    # import functions from *.ipynb files
python -m pip install jupyter_nbextensions_configurator
```

#### c. Setup

Create an alias `load_earthscope` in `~/.bash_profile` file for easy activation, _e.g._:

```bash
alias load_earthscope='conda activate earthscope; source ~/tools/conda_envs/earthscope/config.rc'
```

#### d. Test the installation

```bash
load_earthscope            # wram up the conda environment
topsApp.py -h              # test ISCE-2
ariaDownload.py -h         # test ARIA-tools
smallbaselineApp.py -h     # test MintPy
```

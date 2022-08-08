## Install InSAR data processing softwares for the [2022 UNAVCO short course (ISCE+)](https://www.unavco.org/event/2022-short-course-insar-processing-analysis-isce/)

### 1. Install conda

```bash
mkdir -p ~/tools; cd ~/tools

# download, install and setup (mini/ana)conda
# link: https://docs.conda.io/en/latest/miniconda.html
# for Linux, use Miniconda3-latest-Linux-x86_64.sh
# for macOS, opt 2: curl https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh -o Miniconda3-latest-MacOSX-x86_64.sh
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
bash Miniconda3-latest-MacOSX-x86_64.sh -b -p ~/tools/miniconda3
~/tools/miniconda3/bin/conda init bash
```

Close and restart the shell for changes to take effect.

```bash
conda config --add channels conda-forge
conda install wget git tree mamba --yes
```

### 2. Install ISCE-2, ARIA-tools and MintPy to the `unavco` environment

#### a. Download source code

```bash
cd ~/tools
git clone https://github.com/isce-framework/isce2.git isce2/src/isce2
git clone https://github.com/aria-tools/ARIA-tools.git
git clone https://www.unavco.org/gitlab/unavco_public/ssara_client.git utils/SSARA
git clone https://github.com/yunjunz/conda_envs.git
```

#### b. Create `unavco` environment and install dependencies

```bash
# create new environment
conda create --name unavco --yes
conda activate unavco

# install dependencies
cd ~/tools
mamba install -c conda-forge --file conda_envs/unavco/requirements.txt --file ARIA-tools/requirements.txt --yes

# install dependencies not available on conda-forge
ln -s ${CONDA_PREFIX}/bin/cython ${CONDA_PREFIX}/bin/cython3
python -m pip install ipynb          # import functions from *.ipynb files
python -m pip install platemotion    # use the latest plate motion correction in mintpy
python -m pip install sardem         # download Copernicus DEM
```

#### c. Setup

Create an alias `load_unavco` in `~/.bash_profile` file for easy activattion, _e.g._:

```bash
alias load_unavco='conda activate unavco; source ~/tools/conda_envs/unavco/config.rc'
```

#### d. Test the installation

```bash
load_unavco                # wram up the conda environment
topsApp.py -h              # test ISCE-2
ariaDownload.py -h         # test ARIA-tools
smallbaselineApp.py -h     # test MintPy
```

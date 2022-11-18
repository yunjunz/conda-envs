# conda_envs

Setup InSAR data processing codes on Linux / macOS using `conda` environments.

### 1. Install conda

```bash
mkdir -p ~/tools; cd ~/tools

# download, install and setup (mini/ana)conda
# for Linux, use Miniconda3-latest-Linux-x86_64.sh
# for macOS, opt 2: curl https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh -o Miniconda3-latest-MacOSX-x86_64.sh
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
bash Miniconda3-latest-MacOSX-x86_64.sh -b -p ~/tools/miniconda3
~/tools/miniconda3/bin/conda init bash
```

Close and restart the shell for changes to take effect.

```
# setup conda-forge channel
conda config --add channels conda-forge
conda config --set channel_priority strict

# install utilities
conda install wget git tree mamba --yes

# install utilities not available from conda
# msrsync - multi-stream rsync for file transfer
cd ~/tools
mkdir bin; cd bin
wget https://raw.githubusercontent.com/jbd/msrsync/master/msrsync3 -O msrsync && chmod +x msrsync
```

Add `export PATH=${PATH}:~/tools/bin` to the `~/.bash_profile` file.

### 2. Install ISCE-2, ARIA-tools and MintPy to `insar` environment

Notes below installs ISCE-2 from conda or source, ARIA-tools and MintPy from source in development mode.

#### a. Download source code

```bash
cd ~/tools
mkdir isce2; cd isce2
mkdir build install src; cd src
git clone https://github.com/isce-framework/isce2.git

cd ~/tools
git clone https://github.com/aria-tools/ARIA-tools.git
git clone https://github.com/insarlab/MintPy.git
git clone https://github.com/insarlab/PyAPS.git
git clone https://github.com/insarlab/PySolid.git
git clone https://github.com/yunjunz/conda_envs.git
git clone https://www.unavco.org/gitlab/unavco_public/ssara_client.git utils/SSARA
```

#### b. Install dependencies to `insar` environment

```bash
# create new environment
conda create --name insar --yes
conda activate insar

# opt 1: install isce-2 with conda (for macOS and Linux)
mamba install -y --file conda_envs/insar/requirements.txt --file MintPy/requirements.txt --file ARIA-tools/requirements.txt isce2

# opt 2: install isce-2 from source (for Linux on kamb only)
mamba install -y --file conda_envs/insar/requirements.txt --file MintPy/requirements.txt --file ARIA-tools/requirements.txt --file conda_envs/isce2/requirements.txt

# install MintPy and ARIA-tools in development mode
python -m pip install -e MintPy
python -m pip install -e ARIA-tools
# [optional] overwrite PyAPS and PySolid installation from conda to the local development mode [for developers]
python -m pip install -e PyAPS
python -m pip install -e PySolid

# install dependencies not available from conda
ln -s ${CONDA_PREFIX}/bin/cython ${CONDA_PREFIX}/bin/cython3
python -m pip install scalene   # CPU, GPU and memory profiler
python -m pip install ipynb     # import functions from *.ipynb files
```

<details>
<summary><h4>c. Build and install ISCE-2 from source (for opt 2 ONLY)</h4></summary>

This is for opt 2 (building and installing the development version of ISCE-2 from source) ONLY.

```bash
# load CUDA module on Caltech kamb/HPC
module load cuda/11.2

# generate build system
# before re-run, delete existing contents in build folder
# more notes on https://github.com/lijun99/isce2-install
cd ~/tools/isce2/build
cmake ~/tools/isce2/src/isce2 -DCMAKE_INSTALL_PREFIX=~/tools/isce2/install -DCMAKE_CUDA_FLAGS="-arch=sm_60" -DCMAKE_PREFIX_PATH=${CONDA_PREFIX} -DCMAKE_BUILD_TYPE=Release

# compile and install
# then under the $ISCE_ROOT/install, there should be `bin` and `packages` folder
make -j 16 # use multiple threads to accelerate
make install
```

Add `export ISCE_INSTALL_METHOD="source"` to the `~/.bash_profile` file.
</details>

#### d. Setup

Create an alias `load_insar` in `~/.bash_profile` file for easy activation, _e.g._:

```bash
alias load_insar='conda activate insar; source ~/tools/conda_envs/insar/config.rc'
```

#### e. Test the installation

Run the following to test the installation:

```bash
load_insar               # warm up conda environment
topsApp.py -h            # test ISCE-2
cuDenseOffsets.py -h     # test ISCE-2/PyCuAmpcor (for opt 2 only)
ariaDownload.py -h       # test ARIA-tools
smallbaselineApp.py -h   # test MintPy
```

Run the following for CPU and memory profiler via [`scalene`](https://github.com/emeryberger/scalene) of python script, e.g. `ifgram_inversion.py`:

```bash
scalene --reduced-profile ~/tools/MintPy/mintpy/ifgram_inversion.py ~/data/test/FernandinaSenDT128/mintpy/inputs/ifgramStack.h5 -w no
```

### Useful resources

+ [conda cheatsheet](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf)
+ [Jupyter: Display hidden files](https://jupyterlab.readthedocs.io/en/stable/user/files.html#displaying-hidden-files)

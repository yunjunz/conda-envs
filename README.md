## conda-envs

Setup InSAR data processing codes on Linux / macOS / Windows using `conda` environments.

### 0. (For Windows only) [Install Windows Subsystem for Linux](./docs/wsl.md)

### 1. Install conda

Install the Python environment with `conda/mamba`.

```bash
mkdir -p ~/tools; cd ~/tools
# for macOS, use Miniforge3-MacOSX-x86_64.sh, and optionally use `curl -L -O https://...` syntax to download
wget https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh
bash Miniforge3-Linux-x86_64.sh -b -p ~/tools/miniforge
~/tools/miniforge/bin/mamba init bash
```

Close and restart the shell for changes to take effect. Then install the following utilities:

```
mamba install wget git tree numpy --yes
pip install bypy
```

### 2. Install ISCE-2 and MintPy

#### Option 1: Install isce2 (conda version) and mintpy (conda version)

Both isce2 and mintpy are available on the conda-forge channel and can be install via `mamba` as:

```bash
mamba install isce2 mintpy
```

Add below in your source file, e.g. `~/.bash_profile` for _bash_ users or `~/.cshrc` for _csh/tcsh_ users:

```bash
export PATH=${PATH}:${ISCE_HOME}/bin:${ISCE_HOME}/applications  # ISCE_HOME/STACK are set by conda
```

#### Option 2: Install isce2 (conda version) and mintpy (development version)

##### a. Download source code

```bash
cd ~/tools
mkdir isce2; cd isce2
mkdir build install src; cd src
git clone https://github.com/isce-framework/isce2.git

cd ~/tools
git clone https://github.com/insarlab/MintPy.git
git clone https://github.com/insarlab/PyAPS.git
git clone https://github.com/insarlab/PySolid.git
git clone https://github.com/yunjunz/conda-envs.git

mkdir -p ~/tools/utils; cd ~/tools/utils
git clone https://gitlab.com/yunjunz/SSARA.git
git clone https://github.com/yunjunz/sardem.git
```

##### b. Install to `insar` environment

```bash
# create new environment
conda create --name insar --yes
conda activate insar

# install dependenciues and isce2
cd ~/tools
mamba install --file conda-envs/insar/requirements.txt --file MintPy/requirements.txt isce2">=2.6.3" --yes

# install MintPy in editable mode
python -m pip install -e MintPy
# [for developers] overwrite PyAPS and PySolid installation from conda to the editable mode
python -m pip install -e PyAPS
export SETUPTOOLS_ENABLE_FEATURES="legacy-editable"
python -m pip install -e PySolid

# install dependencies not available from conda
ln -s ${CONDA_PREFIX}/bin/cython ${CONDA_PREFIX}/bin/cython3
python -m pip install ipynb     # import functions from *.ipynb files
python -m pip install jupyter_nbextensions_configurator
python -m pip install -e utils/sardem
```

##### c. Setup

Create an alias `load_insar` in `~/.bash_profile` file for easy activation, _e.g._:

```bash
alias load_insar='conda activate insar; source ~/tools/conda-envs/insar/config.rc'
```

##### d. Test the installation

Run the following to test the installation:

```bash
load_insar               # warm up conda environment
topsApp.py -h            # test ISCE-2
smallbaselineApp.py -h   # test MintPy
```

#### Option 3: [Install isce2 (development version) and mintpy (development version)](./isce2/README.md)

### Useful resources

+ [Linux setup](https://github.com/yunjunz/conda-envs/blob/main/docs/terminal.md)
+ [conda cheat sheet](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf)
+ [Jupyter: Display hidden files](https://jupyterlab.readthedocs.io/en/stable/user/files.html#displaying-hidden-files)
+ [Jupyter: Run Notebooks on a remote server](docs/jupyter.md)
+ [Connect to GitHub with SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
+ [`bypy`: Command line downloader for Baidu Yun](https://blog.csdn.net/PolarisRisingWar/article/details/121887801)

# conda_envs

Setup InSAR data processing codes on Linux / macOS.

### 1. Install conda

```bash
mkdir -p ~/tools; cd ~/tools

# download, install and setup conda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
chmod +x Miniconda3-latest-MacOSX-x86_64.sh
./Miniconda3-latest-MacOSX-x86_64.sh -b -p ~/tools/miniconda3
~/tools/miniconda3/bin/conda init bash
```

Close and restart the shell for changes to take effect.

### 2. Install ISCE-2, ARIA-tools, MintPy and PyAPS to `insar` environment

Setup environment variables by sourcing the [`conda_envs/insar/config.rc`](./insar/config.rc) file, e.g. in [`~/.bash_profile`](./bash_profile.md) file.

Run the following to download the source code and to install their dependencies.

```bash
source ~/tools/conda_envs/insar/config.rc

# install the latest git, if the current git version < 2.0
$CONDA_PREFIX/bin/conda install git

# download source code
cd ~/tools
git clone https://github.com/isce-framework/isce2.git
git clone https://github.com/aria-tools/ARIA-tools.git
git clone https://github.com/insarlab/MintPy.git
git clone https://github.com/yunjunz/pyaps3.git $PYAPS_HOME/pyaps3
git clone https://github.com/yunjunz/conda_envs.git

# create new environment
$CONDA_PREFIX/bin/conda create --name insar
$CONDA_PREFIX/bin/conda activate insar

# install pre-requisites
# gfortran_osx/linux-64 for solid earth tide in MintPy
$CONDA_PREFIX/bin/conda config --add channels conda-forge
$CONDA_PREFIX/bin/conda install gfortran_linux-64 --file conda_envs/insar/requirements.txt --file MintPy/docs/conda.txt --file ARIA-tools/requirements.txt
$CONDA_PREFIX/bin/pip install git+https://github.com/tylere/pykml.git
```

Run the following to test the installation:

```bash
topsApp.py -h
ariaDownload.py -h
smallbaselineApp.py -v
```

### 3. Install FRInGE to the `fringe` environment

Modify the install directory in **conda_envs/fringe/config.rc** file.

```bash
# create new environment
conda create --name fringe
conda activate fringe

# install pre-requisites
$CONDA_PREFIX/bin/conda install --yes --file tools/conda_envs/fringe/requirements.txt

# install from source code
source conda_envs/fringe/config.rc
mkdir FRInGE; cd FRInGE
mkdir install build src

cd src
git clone https://github.com/isce-framework/fringe.git

cd ../build
cmake -DCMAKE_INSTALL_PREFIX=../install ../src/fringe
make -j 8 all
make install
```

Test installation:

```bash
sequential.py -h
```

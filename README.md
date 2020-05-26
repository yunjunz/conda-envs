# conda_envs

Setup InSAR data processing codes on Linux OS

### 1. Install conda

```bash
mkdir tools; cd tools

# download, install and setup conda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
chmod +x Miniconda3-latest-Linux-x86_64.sh
./Miniconda3-latest-Linux-x86_64.sh -b -p ~/tools/miniconda3
~/tools/miniconda3/bin/conda init bash
```

Logout and login again, to enter the `base` environment.

### 2. Install ISCE, ARIA-tools, MintPy and PyAPS

Modify the install directory in **conda_envs/insar/config.rc** file.

```bash
# install the latest git
$CONDA_PREFIX/bin/conda install git

# install pre-requisites
$CONDA_PREFIX/bin/conda install --yes --file conda_envs/insar/requirements.txt
$CONDA_PREFIX/bin/pip install --yes git+https://github.com/tylere/pykml.git

# download source code
source conda_envs/insar/config.rc
git clone https://github.com/isce-framework/isce2.git
git clone https://github.com/aria-tools/ARIA-tools.git
git clone https://github.com/insarlab/MintPy.git
git clone https://github.com/yunjunz/pyaps3.git $PYAPS_HOME/pyaps3
```

Test installation:

```bash
topsApp.py -h
ariaDownload.py -h
smallbaselineApp.py -v
```

### 3. Install FRInGE

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

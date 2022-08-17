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

## ISCE2/topsStack demonstration

### 1. Installation

```bash
# load the conda environment
conda activate unavco

# setup path for topsStack
export PATH=${PATH}:${ISCE_STACK}/topsStack

# test
stackSentinel.py -h
```

### 2. Setup auxliary directory

```bash
cd ~/bak
mkdir aux; cd aux
mkdir aux_cal aux_poeorb
```

### 3. Download S1 data from ASF using the web browser or SSARA

SNWE: 32.7,35,-117,-114.5

```bash
cd ~/data-marmot/test/SaltonSeaSenDT173
mkdir SLC; cd SLC
~/tools/utils/SSARA/ssara_federated_query.py -p SENTINEL-1A,SENTINEL-1B -r 173 -b '32.7,35,-117,-114.5' --print
~/tools/utils/SSARA/ssara_federated_query.py -p SENTINEL-1A,SENTINEL-1B -r 173 -b '32.7,35,-117,-114.5' --kml
~/tools/utils/SSARA/ssara_federated_query.py -p SENTINEL-1A,SENTINEL-1B -r 173 -b '32.7,35,-117,-114.5' --download --parallel=4
```

### 4. DEM

```bash
cd ~/data-marmot/test/SaltonSeaSenDT173
mkdir DEM; cd DEM
sardem --bbox -118 31 -113 36 --data COP -isce
# sardem --bbox -118 31 -113 36 --data COP -isce -o copernicus.dem   # pre-stage
# equivalent to $ISCE_HOME/applications/dem.py
```

### 5. Run stackSentinel.py

NOTE: same SNWE/AOI/bbox should be used for "3. data search/download" and "5. run"

```bash
cd ~/data-marmot/test/SaltonSeaSenDT173
stackSentinel.py -s ./SLC -o ~/bak/aux/aux_poeorb -a ~/bak/aux/aux_cal --dem ./DEM/copernicus.dem --coregistration geometry -n '1 2 3' --bbox '32.7 35 -117 -114.5' -W interferogram -c 3 --azimuth_looks 5 --range_looks 15 -f 0.5 -u snaphu
```

Show the `run_files` and `configs` folder, and how to execute the run files.

Show the final results in `~/data-marmot/offset4motion/SaltonSeaSenDT173/merged`. This point is equivalent to the ariaDownload/TSsetup result.

Show the mintpy input example on readthedocs website.

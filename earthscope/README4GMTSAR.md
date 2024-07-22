## Installation for the 2024 EarthScope short course with GMTSAR and MintPy

### 1. [Install conda](../README.md#1-install-conda)

### 2. Install MintPy (development version)

#### a. Download source code

```bash
# i. download MintPy source code
cd ~/tools
git clone https://github.com/insarlab/MintPy.git

# ii. download MintPy tutorial in notebooks
cd ~/tools/utils
git clone https://github.com/yunjunz/MintPy-tutorial.git
```

#### b. Create a Python environment and install dependencies

```bash
# i. create a Python environment
conda create --name mintpy
conda activate mintpy

# ii. install dependencies into the environment
# run "conda install -c conda-forge mamba" if mamba is not installed yet.
cd ~/tools/
mamba install -c conda-forge --file ./MintPy/requirements.txt gdal"==3.6" jupyterlab ipympl pv python"==3.11"
```

#### c. Install MintPy

```bash
python -m pip install -e ~/tools/MintPy/
```

### 3. Test MintPy with example dataset

```bash
cd ~/data/test
wget https://zenodo.org/record/3952953/files/FernandinaSenDT128.tar.xz
tar -xvJf FernandinaSenDT128.tar.xz
cd FernandinaSenDT128/mintpy
smallbaselineApp.py ~/tools/MintPy/docs/templates/FernandinaSenDT128.txt
```

### 4. Run MintPy notebook on the pre-staged GMTSAR dataset

#### a. Download the pre-staged dataset

Download the pre-staged compressed `SanFranBaySenD42.tar.xz` file into your local `~/data/test` directory from [Zenodo](https://zenodo.org/records/12773014) as below or from the USTC website :

```bash
cd ~/data/test
wget https://zenodo.org/records/12773014/files/SanFranBaySenD42.tar.xz
```

#### b. Start the Jupyter Notebook

```bash
cd ~
jupyter-lab
```

### c. Open the notebook and run

In the notebook browser, navigate to `~/tools/utils/MintPy-tutorial/workflows` directory on the left sidebar, then open the `smallbaselineApp_gmtsar.ipynb` file and run.

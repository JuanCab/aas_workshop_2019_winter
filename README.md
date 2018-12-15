# aas_workshop_2019_winter
Workshop resources for winter AAS meeting 2019
# Configuring Python Environment
These directions walk through installing miniconda, a lightweight distribution of the python package installer conda, and then creating a custom environment for the NAVO workshop. If you already have a conda distribution installed skip to section 2. If you have python installed, but not conda, and do not wish to install conda, skip to section 3. (Everyone else can skip section 3).
## Section 1: Installing miniconda
### Command­-line instructions for Mac and Linux
First, download the appropriate installation script by running the following commands **in a bash shell**:

**Mac**

    wget --no-check-certificate https://repo.continuum.io/miniconda/Miniconda3-latest-MacOSX-x86_64.sh -O conda_latest.sh

**Linux**

    wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O conda_latest.sh

To install miniconda globally, run the following commands (this assumes you have permission to install in system directories):

    bash conda_latest.sh
    rm conda_latest.sh

To install miniconda locally, run the following commands in the directory you wish to install it in (no special permissions are required for this method):

    bash conda_latest.sh -b -p $PWD/conda
    rm conda_latest.sh
    export PATH=$PWD/conda/bin:$PATH

*If you want to add conda to your path permanently you will need to add the export PATH line to your ~/.profile or ~/.bashrc file (with $PWD replaced by the actual path to your conda installation).
### Instructions for Windows
* Download a Python 3.6 installer:
  * https://repo.continuum.io/miniconda/Miniconda3-latest-Windows-x86_64.exe (64-bit)
  * https://repo.continuum.io/miniconda/Miniconda3-latest-Windows-x86.exe (32-bit)
* Double click the .exe files and follow the instructions on the screen.
* To access conda and Python you will use the "Anaconda Prompt" which you can find by opening the start menu and searching.
## Section 2: Creating a custom Python environment for the workshop
Run the following commands **in a bash shell** (or on Windows in the Anaconda Prompt):

    # Create conda environment called 'navo' with basic libraries
    # (When prompted, answer 'y' to ok the installation.)
    conda create --name navo python=3.6 astropy matplotlib notebook requests scipy  
    
    # Activate the new environment. (Any time you want to use this
    # environment, you will first need to activate it.) 
    conda activate navo

    # Install astroquery. 
    # (When prompted, answer 'y' to ok the installation.)
    conda install -c astropy astroquery

    # Install aplpy (needed for some plotting in the examples).
    pip install aplpy

## Section 3: Required package list
If you already have python but not conda and do not wish to install conda, you will need to make sure you have an environment with the following packages (and their dependencies) installed:
* astropy
* astroquery
* matplotlib
* notebook
* requests
* scipy
* aplpy
## Section 4: Test your environment
The Python notebook [test_imports.ipynb](https://github.com/NASA-NAVO/aas_workshop_2019_winter/blob/master/test_imports.ipynb) includes all relevant imports to test your environment.  

Using a web browser (or wget as above), download the [raw notebook file](https://raw.githubusercontent.com/NASA-NAVO/aas_workshop_2019_winter/master/test_imports.ipynb) and save it as `test_imports.ipynb`.  (Ensure that the file extension is `.ipynb` as the browser may try to save it as `.txt`.)

To run this notebook, activate your NAVO environment (conda activate navo), then run the following command from the same directory as the notebook:

    jupyter notebook test_imports.ipynb

The notebook should open in a browser window. Run the first cell by positioning your cursor in the cell and typing shift+enter. You should get no errors or output. 
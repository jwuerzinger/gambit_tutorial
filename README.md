# GAMBIT tutorial

This repo collects a few simple things required for running GAMBIT, particularly in the docker image.
Please let me know if you have any issues running this whatsoever!

Please clone this repo with:
```bash
git clone --recurse-submodules git@github.com:jwuerzinger/gambit_tutorial.git
```
to also get the main gambit repo containing example `yaml` files.

# Setup / Installation

1. Install [docker](https://www.docker.com)
2. Set up a python environment for analysing results:
   1. Install [miniconda](https://docs.anaconda.com/miniconda/) (anaconda works too, if you have that installed already).
   2. Make a new environment with `conda create --name gambitenv --file requirements.txt`
   3. Activate the environment with `conda activate gambitenv`

# Running GAMBIT

Before running the docker image, create and empty folder to store scan results

```bash
mkdir runs
```

After that, run the docker image with:

## Run gambit docker image with:
```bash
docker run -v $PWD/gambit/yaml_files/:/yaml_files/ -v $PWD/runs/:/runs -it --rm gambitbsm/gambit-pippi
```

After entering the container, you can run an example scan of the [Wilson coefficients](https://gambitbsm.org/tutorials/TRIUMF/GAMBIT_Tutorial_Part_2.pdf). 
Run the example scan with:

```bash
./gambit -f /yaml_files/WC_lite.yaml
```

GAMBIT will put your scan results in a local folder (`runs/`). Make sure you copy the contents of this folder into `/runs` (note the different placement of the forward slash!) before exiting the docker image.

## Making plots

[test.ipynb](test.ipynb) collects some simple ways of making plots for a scan. Use this as a draft for looking at scan results. Use the conda environment's kernel here and everything *should* work out-of-the box.

### Make plots with pippi:

If you can make the GAMBIT plotting framework [pippi](https://github.com/tegonzalo/pippi) work, you can make plots by simply calling:
```
pippi /yaml_files/spartan.pip
```
I haven't managed to do this yet though, so standalone python is fine :)

## Running a scan for the MSSM:

If you want to run larger scans for the EWK pMSSM, use the yaml file:
```
./gambit -f /yaml_files/MSSM7.yaml
```
This is a very large scan though, so make sure to increase the convergence threshold and reduce the number of parallel processes:

```yaml
    de:
      plugin: diver
      like: LogLike
      # NP: 19200
      # convthresh: 1e-5
      NP: 1
      convthresh: 1e-1
      verbosity: 1
```
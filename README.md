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
Change the scan config to save the scan results as `hdf5` with:
```
Printer:

  printer: hdf5
  options:
   output_file: "WC.hdf5"
   group: "/WC"

  # printer: ascii
  # options:
  #   output_file: "WC.dat"
  #   buffer_length: 100
  #   delete_file_on_restart: true
```
Run the example scan with:

```bash
./gambit -f /yaml_files/WC_lite.yaml
```

The scan will print a lot of stuff, including likelihoods and parameters at each point. You'll know that the scan was successful if it prints something like this:
```
Total log-likelihood: -1.3252244

Diver run finished!
ScannerBit is waiting for all MPI processes to report their shutdown condition...
Final dataset size is 7600

GAMBIT has finished successfully!

Calling MPI_Finalize...
```

GAMBIT will put your scan results in a local folder (`runs/`). This folder should look like this:
```
runs/WC_lite/
├── WC_lite.yaml
├── logs
│   ├── FlavBit.log
│   ├── debug.log
│   └── default.log
├── samples
│   └── WC.hdf5
└── scanner_plugins
    └── Diver
        ├── native.devo
        ├── native.raw
        └── native.rparam
```
Feel free to inspect all files, in particular the `.log` files, which contain the output logs of all parts used in the scan.
Make sure you copy the contents of this folder into `/runs` (note the different placement of the forward slash!) before exiting the docker image: `mv runs/* /runs/`

## Making plots

[test.ipynb](test.ipynb) collects some simple ways of making plots for a scan. Use this as a draft for looking at scan results. Use the conda environment's kernel here and everything *should* work out-of-the box.

### Make plots with pippi:

If you can make the GAMBIT plotting framework [pippi](https://github.com/tegonzalo/pippi) work, you can make plots by simply calling:
```
pippi /yaml_files/WC_lite.pip
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

Question for you: Which parts of the scan could we disable here?
# Gulf of Mexico Temperature Wind Change

## Project Description

In this project, I will investigate the effects of wind forcing on ocean temperature in the Gulf of Mexico. In particular, I will investigate the following scientific question:

*How do winds affect ocean temperature in the Gulf of Mexico Current?*

To investigate this question, I will construct a model domain encompassing the Gulf of Mexico and run my model simulation for one year in two experiments: a run with wind forcing and a run without wind forcing. The grid for my model will be located in the Gulf of Mexico, capturing key features such as the Loop Current and surrounding coastal regions. I will run my experiment during the year 2017, which is the most current year. I anticipate that the model with wind forcing will show more variability and cooler surface temperatures in certain regions compared to the model without wind, due to enhanced vertical mixing and surface heat flux changes.

<!-- 2017 experienced notable wind-driven variability according to NCEP reanalysis data ??-->

For initial conditions, I will use the state of the ECCO Version 5 Model in January 2017. Similarly, I will construct boundary and external forcing conditions for this model from the ECCO Version 5 model output. To analyze the results, I will create a time series of temperatures in the region near the Loop Current and investigate differences between the two experiments over time. For visualization, I will make a movie of temperature differences between the model with wind and without wind.

## Reproducing Model Results

The following steps outline how to construct the model files, configure and run the model, and assess the model results.

### Step 1: Create the Model Files
Several input files need to be created to run the model. Generate the following list of files using the notebooks indicated in paratheses:
- Model Grid (notebooks/Creating the Model Grid.ipynb)
- Bathymetry (notebooks/Creating the Bathymetry.ipynb)
- Initial Conditions (notebooks/Creating the Initial Conditions.ipynb)
- External Forcing Conditions (notebooks/Creating the External Forcing Conditions.ipynb)
- Boundary Conditions (notebooks/Creating the Boundary Conditions.ipynb)
The model files should be placed into the  `input` directory.

### Step 2: Add files to the computing cluster
Once the input files have been created, the model files can be transferred to the computing cluster. Begin by cloning a copy of [MITgcm](https://github.com/MITgcm/MITgcm) into your scratch directory and make a folder for the configuration, .e.g.
```
mkdir MITgcm/configurations/GoM
```
Then, use the `scp` command to send the `code`, `input`, and `namelist` directories to your configuration directory. Run in your local directory. Example:
```
scp -r [username]@spartan03.sjsu.edu:/scratch/[username]/MITgcm/configurations/[model name]/input
```

### Step 3: Compile the model
Once all of the files are on the computing cluster, the model can be compiled. Make a `build` directory in the configuration directory and run the following lines:
```
../../../tools/genmake2 -of ../../../tools/build_options/darwin_amd64_gfortran -mods ../code -mpi
make depend
make
```

### Step 4.1: Run the model with wind
After the compilation is complete, run the model without the wind. Move to the run directory, link everything from `input` and `code`. 
```
cd ../run
ln -s ../input/* .
ln -s ../build/mitgcmuv .
```

Create the job script: (make sure # ntasks = # mpirun -np)
```
>cat username.slm
#!/bin/bash
#SBATCH --partition=nodes
#SBATCH --nodes=1
#SBATCH --ntasks=16
#SBATCH --time=10:00:00
export MPI_HOME=/home/[username]/.conda/envs/cs185c
mpirun -np 16 ./mitgcmuv
```
And, run job script:
```
sbatch username.slm
```

### Step 4.2: Run the model without wind
Next, run the model with wind to complete the experiment. Again, link everything from `input` and `code` to a directory called `run_with_wind`. Then, edit the `data.exf` file to point to the modified wind files (see the Creating the External Forcing Conditions.ipynb notebook for details). Then, submit the job script again to rerun the model.

### Step 5: Analyze the Results
There are three notebooks provided for analysis:
1. Analyzing Model Results

   These notebooks are provided to have a quick look at spatial and temporal variations in the temperature field in the model with wind and without wind. It also generates the visualization provided in the figures directory.
   
2. Answering the Science Question
   
   This notebooks provided some analysis plot to address the science question posed above.

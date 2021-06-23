---
title: Energy minimisation
teaching: 10
exercises: 0
questions:
- "What should I do after generating my system's topology and coordinates?"
- "How do I refine and remove bad contacts from the initial structure?" 
objectives:
- "Run a `sander` command to minimise the initial structure."
- "Explain briefly the options in the sander input file."
keypoints:
- "After adding water molecules and combining coordinates of different system elements we have to minimise the system to fix any bad contacts (LEap had warned us already of some bad contacts in the structure)." 
- "Removing bad contacts at this point will prevent our system from failing catastrophically later down the line."
- "Combining different minimisation methods is a good practice and can help to avoid getting stuck into a local minima."
--- 

Before running any QM/MM simulation, we must **ALWAYS** equilibrate the system using only molecular mechanics (MM). This step will take approx. 20 min, so we are going to submit the calculation NOW on the short queue of ARCHER using the following script:

~~~
qsub send_mm_equil.pbs
~~~
{: .language-bash}

We are going to minimise and equilibrate the system using the `sander` tool from AmberTools suite. We have provided commented input files and ARCHER submission scripts to make these steps easier to follow. You can find more information on the sander input options in [Chapter 19](https://ambermd.org/doc12/Amber20.pdf) of the Amber20 manual.

To run `sander`, one needs to specify at least the initial coordinates (`-c system.rst7`) and the topology files of your system (`-p system.parm7`) and an input file (`-i sander_min.in`) providing the details of the simuilation to perform. Also, we can specify the name of output files such the log file (`-o`) and final coordinates (`-r`). 

~~~
#!/bin/bash --login
#PBS -N MM_equil

# Select 1 nodes (maximum of 24 cores)
#PBS -q short
#PBS -l select=2
#PBS -l walltime=00:20:00

# Make sure you change this to your budget code
#PBS -A y14-amber2020

module swap PrgEnv-cray PrgEnv-gnu
module load amber-tools/20

# Move to directory that script was submitted from
export PBS_O_WORKDIR=$(readlink -f $PBS_O_WORKDIR)
cd $PBS_O_WORKDIR

date
echo "Minimisation"
aprun -n 8 sander.MPI -O -i sander_min.in -o min_classical.out -p system.parm7 -c system.rst7 -r system.min.rst7
date
echo "Thermalisation"
aprun -n 48 sander.MPI -O -i sander_heat.in -o heat_classical.out -p system.parm7 -c system.min.rst7 -r system.heat.rst7 -x system.heat.nc
date
echo "Pressure equilibration"
aprun -n 48 sander.MPI -O -i sander_equil.in -o equil_classical.out -p system.parm7 -c system.heat.rst7 -r system.equil.rst7 -x system.equil.nc
date
~~~
{: .source}

***

## Energy minimisation with `sander`

We have devised a minimisation protocol that will perform 4000 steps using two different methods: 2000 of [gradient descent](https://en.wikipedia.org/wiki/Gradient_descent) and 2000 of [conjugate gradient](https://en.wikipedia.org/wiki/Conjugate_gradient_method). The sander input file looks like this:

~~~
Minimisation of system 
 &cntrl
  imin=1,        ! Perform an energy minimization.
  maxcyc=4000,   ! The maximum number of cycles of minimization. 
  ncyc=2000,     ! The method will be switched from steepest descent to conjugate gradient after NCYC cycles.
 /
~~~
{: .source}

`sander` should have produced the following files: 
- `min_classical.out` where it has written down the energy of the system every 50 steps.
- `system.min.rst7` with the minimised coordinates. 

If we open the `min_classical.out` file, the last step of the minimisation should look like this:

~~~
   NSTEP       ENERGY          RMS            GMAX         NAME    NUMBER
   4000      -1.7025E+05     2.0459E-01     9.4107E+00     OE1       169

 BOND    =    13362.4614  ANGLE   =      412.0010  DIHED      =     2157.5043
 VDWAALS =    34829.2930  EEL     =  -229750.9423  HBOND      =        0.0000
 1-4 VDW =      587.3195  1-4 EEL =     8150.8146  RESTRAINT  =        0.0000
~~~
{: .output}

We want the system to relax, so the `ENERGY` will decrease along the energy minimisation but we should also take the `GMAX` value into account. The `GMAX` is the slope on the Potential Energy Surface (PES) of the system of the current coordinates. `GMAX` should be close to 0. Other important information is the `NAME` and `NUMBER`, they point to the atom name and index, that is causing the major energy contribution.  

After the minimisation step, we will heat the system up to the target temperature 298K (25°C, standard value for room temperature experiments) while keeping the volume constant. Afterwards, we will allow the volume to fluctuate and equilibrate the density of the system at a contant pressure of 1 atm (standard value) while still keeping the temperature at 298K.  

## Thermalisation

We will use the input file `sander_heat.in` to gradually increase the temperature of the system up to our target value over the first 30 ps. To help our system accomodate to this change, we will use a temperature ramp in which we control the temperaure increase per timestep.

~~~
Heating ramp from 0K to 300K 
 &cntrl
  imin=0,                   ! Run molecular dynamics.
  ntx=1,                    ! Initial file contains coordinates, but no velocities.
  irest=0,                  ! Do not restart the simulation, (only read coordinates from the coordinates file)
  nstlim=15000,             ! Number of MD-steps to be performed.
  dt=0.002,                 ! Time step (ps)
  ntf=2, ntc=2,             ! Constrain lengths of bonds having hydrogen atoms (SHAKE)
  tempi=0.0, temp0=298.0,   ! Initial and final temperature
  ntpr=500, ntwx=500,       ! Output options
  cut=8.0,                  ! non-bond cut off
  ntb=1,                    ! Periodic conditiond at constant volume
  ntp=0,                    ! No pressure scaling
  ntt=3, gamma_ln=2.0,      ! Temperature scaling using Langevin dynamics with the collision frequency in gamma_ln (ps−1)
  ig=-1,                    ! seed for the pseudo-random number generator will be based on the current date and time.
  nmropt=1,                 ! NMR options to give the temperature ramp.
 /
&wt type='TEMP0', istep1=0, istep2=12000, value1=0.0, value2=298.0 /
&wt type='TEMP0', istep1=12001, istep2=15000, value1=298.0, value2=298.0 /
&wt type='END' /
~~~
{: .source}

`sander` produces several output files:
- `heat_classical.out`: Log file with system values stored during the run. 
- `system.heat.rst7`: coordinates and velocities to restart the simulation.
- `system.heat.nc`: trajectory in netcdf format of the simulation.

If we open the `heat_classical.out` file, we will find that the last step of MD has similar values as these (they won't be the same as we have generated velocities with a random number seed):

~~~
 NSTEP =    15000   TIME(PS) =      30.000  TEMP(K) =   298.10  PRESS =     0.0
 Etot   =   -156706.7596  EKtot   =     39190.4089  EPtot      =   -195897.1685
 BOND   =       919.5864  ANGLE   =      2384.5882  DIHED      =      3850.0906
 1-4 NB =      1089.6233  1-4 EEL =     12032.6825  VDWAALS    =     24316.3039
 EELEC  =   -240490.0435  EHBOND  =         0.0000  RESTRAINT  =         0.0000
 Ewald error estimate:   0.3447E-04     
~~~
{: .output}

## Pressure equilibration

Once the thermalisation step has finished, `sander` will use the input file `sander_equil.in` to equilibrate the density of the system. It is advised to heat and equilibrate over longer periods of simulations than the ones used in this workshop. However for the sake of time, we will use the same simulation length (30ps):

~~~
Density equilibration
&cntrl
  imin= 0,                       ! Run molecular dynamics.
  nstlim=15000,                  ! Number of MD-steps to be performed.
  dt=0.002,                      ! Time step (ps)
  irest=1,                       ! Restart the simulation and read coordinates and velocities from the restart file provided in -c
  ntx=5,                         ! Initial file contains coordinates and velocities.
  ntpr=500, ntwx=500, ntwr=500,  ! Output options
  cut=8.0,                       ! non-bond cut off
  temp0=298,                     ! Temperature
  ntt=3, gamma_ln=3.0,           ! Temperature scaling using Langevin dynamics with the collision frequency in gamma_ln (ps−1)
  ntb=2,                         ! Periodic conditiond at constant pressure
  ntc=2, ntf=2,                  ! Constrain lengths of bonds having hydrogen atoms (SHAKE)
  ntp=1, taup=2.0,               ! Pressure scaling
  iwrap=1, ioutfm=1,             ! Output trajectory options
/
~~~
{: .source}

Again, `sander` produces several output files:
- `equil_classical.out`: Log file with thermodynamic data of the system stored during the run.
- `system.equil.rst7`: coordinates and velocities to restart the simulation.
- `system.equil.nc`: trajectory in netcdf format of the simulation.

The last step of MD in the `equil_classical.out` should be similar to this: 

~~~
 NSTEP =    15000   TIME(PS) =      60.000  TEMP(K) =   300.17  PRESS =   -89.5
 Etot   =   -160777.6918  EKtot   =     39462.5280  EPtot      =   -200240.2199
 BOND   =       868.6881  ANGLE   =      2429.6473  DIHED      =      3839.3079
 1-4 NB =      1101.9515  1-4 EEL =     12099.2533  VDWAALS    =     25814.3482
 EELEC  =   -246393.4160  EHBOND  =         0.0000  RESTRAINT  =         0.0000
 EKCMT  =     18077.2732  VIRIAL  =     19336.2993  VOLUME     =    651748.7327
                                                    Density    =         1.0087
~~~
{: .output}

****

## Analysis of the MM equilibration simulation

We will now have a closer look at the final steps highlighted before. In the log files, we have monitored the values of several quantities. Especially relevant are:
- `NSTEP`: The time step that the MD simulation is at
- `TIME`: The total time of the simulation (including restarts)
- `TEMP`: System temperature
- `PRESS`: System pressure
- `Etot`: Total energy of the system
- `EKtot`: Total kinetic energy of the system
- `EPtot`: Total potential energy of the system 

AmberTools provides `process_mdout.perl`, a perl script to gather and print this information in a easy-to-use format. You can easily call it from the command-line like this:  

~~~
$AMBERHOME/bin/process_mdout.perl heat_classical.out equil_classical.out
~~~
{: .language-bash}

~~~
Processing sander output file (heat_classical.out)...
Processing sander output file (equil_classical.out)...
Starting output...
Outputing summary.TEMP
Outputing summary.TSOLUTE
Outputing summary.TSOLVENT
Outputing summary.PRES
Outputing summary.EKCMT
Outputing summary.ETOT
Outputing summary.EKTOT
Outputing summary.EPTOT
Outputing summary.DENSITY
Outputing summary.VOLUME
~~~
{: .output}

Here we will show you the expected output of an equilibration run. First, we will show the temperature and the energies (kinetic, potential and total) of the system. 

| Temperature | Energy |
| ------------- | ------------- |
| K | kcal/mol |
| ![Temperature over time]({{ page.root }}/fig/temperature.png)  | ![Energy over time]({{ page.root }}/fig/energy.png)  |

The pressure and the density are only set when we perform the NPT run, therefore they only show reasonable values on the last 30 ps. 

| Pressure | Density |
| ------------- | ------------- |
| bar | g*cm-3 |
| ![Pressure over time]({{ page.root }}/fig/pressure.png)  | ![Density over time]({{ page.root }}/fig/density.png)  |


You can see that the system starts to reach a plateau on each one of these values. We want to reiterate that the MM equilibration in this tutorial is way too short (only 60ps) and we suggest you to run a longer equilibration steps in your simulations. 

It is not within the scope of this tutorial to fully analyse these equilibration runs, therefore we leave that to you. However, we provide a jupyter notebook with the code to produce these graphs for your systems.

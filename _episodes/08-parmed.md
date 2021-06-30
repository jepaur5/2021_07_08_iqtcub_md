---
title: Production runs
teaching: 10
exercises: 0
questions:
- "How long should I run my production runs?"
objectives:
- "Performed production runs using `pmemd`."
keypoints:
- "Production runs are usually run in an HPC or local cluster environments." 
- "The length and number of replicates of your runs depends deeply on the hypotheses you want to prove." 
---

## Production runs

Finally, we are moving forward to the production runs, which are usually performed in an HPC environment. Therefore we won't use these workstations to run the simulations, but we will go over the files needed to run these simulations and will use a simulation we've run for you. 

The `mdin` of the production runs is very similar to the `equil_classical.mdin` but no restraints are applied this time. We will perform 5 runs of 10ns. 

~~~
Production runs
&cntrl
  imin= 0,                         ! Run molecular dynamics.
  nstlim=5000000,                  ! Number of MD-steps to be performed.
  dt=0.002,                        ! Time step (ps)
  irest=1,                         ! Restart the simulation and read coordinates and velocities from the restart file provided in -c
  ntx=5,                           ! Initial file contains coordinates and velocities.
  ntpr=1000, ntwx=1000, ntwr=1000, ! Output options
  cut=8.0,                         ! non-bond cut off
  temp0=298,                       ! Temperature
  ntt=3, gamma_ln=3.0,             ! Temperature scaling using Langevin dynamics with the collision frequency in gamma_ln (ps−1)
  ntb=2,                           ! Periodic conditiond at constant pressure
  ntc=2, ntf=2,                    ! Constrain lengths of bonds having hydrogen atoms (SHAKE)
  ntp=1, taup=2.0,                 ! Pressure scaling
  iwrap=1, ioutfm=1,               ! Output trajectory options
/
~~~
{: .source}

We execute the simulations using `pmemd.MPI` which uses similar options as `sander`. These commands may change slightly depending on the system you use to run the simulations. 

~~~
mpirun -np 48 $AMBERHOME/bin/pmemd.MPI -O -i mdin -p system.parm7 -c system.md0.rst7 -x system.md1.nc -r system.md1.rst7 -e system.md1.ene -o system.md1.out -ref system.md0.rst7 -inf system.md1.info
~~~
{: .language-bash}

The resulting files are the same than before, `sander` produces several output files:
- `system.md1.out`: Log file with thermodynamic data of the system stored during the run.
- `system.md1.rst7`: coordinates and velocities to restart the simulation.
- `system.md1.nc`: trajectory in netcdf format of the simulation.
- `system.md1.info`: Information on the performance of the simulation.

## Usual points to take into account when running MD simulations

#### Replicates

#### Length of the simulation

#### NVT or NPT ensembles






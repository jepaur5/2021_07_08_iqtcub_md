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

Before running any simulation, we must **ALWAYS** minimise and equilibrate the system. We are going to minimise and equilibrate the system using the `sander` tool from AmberTools suite. We have provided commented input files and ARCHER submission scripts to make these steps easier to follow. You can find more information on the sander input options in [Chapter 19](https://ambermd.org/doc12/Amber20.pdf) of the Amber20 manual.

To run `sander`, one needs to specify at least the initial coordinates (`-c system.rst7`) and the topology files of your system (`-p system.parm7`) and an input file (`-i sander_min.in`) providing the details of the simuilation to perform. Also, we can specify the name of output files such the log file (`-o`) and final coordinates (`-r`). 


## Energy minimisation 

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

To perform the minimisation, you should type the following command:
~~~
sander -O -i sander_min.in -o min_classical.out -p system.parm7 -c system.rst7 -r system.min.rst7
~~~
{: .language-bash}


`sander` would produced the following files: 
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


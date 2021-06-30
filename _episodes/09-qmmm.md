---
title: Basic analysis of the trajectories
teaching: 10
exercises: 0
questions:
- "Has my simulation converged?"
- "Is my protein stable?"
objectives:
- "Visualisation of MD trajectories."
- "Basic analysis of MD trajectories."
keypoints:
- "It is important to recenter the protein to avoid analysis artefacts."
- "It is ALWAYS good practice to visualise your simulations with VMD or Pymol."
- "RMSD of the backbone a protein gives us a measure of its stability."
---

## Recentering the solutes in the simulation box

First of all, we need to recenter the protein to the center of the simulation box using `cpptraj`. `cpptraj` is the trajectory analysis tool of the AmberTools package and it can do a lot of different analysis (you can find more information [here](https://amber-md.github.io/cpptraj/CPPTRAJ.xhtml)). 

First, we need to load the topology file:

~~~
cpptraj -p system_LJ_mod.parm7
~~~
{: .language-bash}

Then, we provide `cpptraj` three different commands: 
- `trajin`: input trajectories. It can be repeated as many times as trajectory file you want to analyse. 
- `trajout`: output trajectories specifying the desired format.
- `autoimage`: recenters protein coordinates and fixes the simulation box around it. 

After you have typed all your analysis commands you have to type `go` or `run` to actuallt make cpptraj run the analysis. 

~~~
trajin system.md1.nc 1 100 10
trajin system.md2.nc 1 100 10
trajin system.md3.nc 1 100 10
trajin system.md4.nc 1 100 10 
trajin system.md5.nc 1 100 10
autoimage
trajout system.MDshort.dcd dcd
go 
quit
~~~
{: .source}


***

## Visualise the shortened trajectory using VMD



***

## Some basic analysis of the simulation: RMSD and RMSF

~~~
trajin system.md1.nc 
trajin system.md2.nc 
trajin system.md3.nc 
trajin system.md4.nc 
trajin system.md5.nc 
autoimage
rms 
atomicfluct
go 
quit
~~~
{: .source}





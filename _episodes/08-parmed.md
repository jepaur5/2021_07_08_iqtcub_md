---
title: Production runs
teaching: 10
exercises: 0
questions:
- "Does the MM forcefield have all the parameters to run a QM/MM simulation?"
objectives:
- "Find missing parameters in the AMBER forcefields using `parmed`."
keypoints:
- "In the MM forcefields, hydrogen atoms do not have Lennard-Jones parameters." 
---

## Adding Lennard-Jones parameters using `parmed`

By construction, some hydrogen atoms do not have Lennard-Jones parameters in the AMBER forcefields. However, before running any QM/MM simulation, we need to provide those parameters. In particular, we are going to add the values of the hydrogen atom that correspond to the hydroxyl in the GAFF forcefield to TIP3P water model and to the hydroxyl groups from serine and tyrosine residues. 

To do that, we will use the `parmed` tool of the AmberTools package. The usage of this program is similar to LEap. First, we load the topology file in `parmed` and then we do have a lot of options to modify it (More information [here](http://parmed.github.io/ParmEd/html/index.html)).





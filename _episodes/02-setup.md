---
title: Log in and environment set up in ARCHER 
teaching: 5
exercises: 0
questions:
- "How do I access ARCHER?"
- "How do I set up the correct environment in ARCHER?"
objectives:
- "Log in to ARCHER."
- "Load AmberTools module in ARCHER."
keypoints:
- "SSH to ARCHER"
- "Load Ambertools with `module load`."
---


In this session we are going to use several software packages:  

- [AmberTools](https://ambermd.org/AmberTools.php) (We will use the latest version of Ambertools. If you are using an older version, commands might run slightly different.)
- [PROPKA](https://github.com/jensengroup/propka-3.1) or [PROPKA server](http://server.poissonboltzmann.org/pdb2pqr) 
- [OpenBabel](http://openbabel.org/wiki/Main_Page)
- [Pymol](https://sourceforge.net/projects/pymol/) or [VMD](https://www.ks.uiuc.edu/Research/vmd/) or another molecular visualisation tool of your preference.
- [Marvin Sketch](https://chemaxon.com/products/marvin).


***

## Downloading data

You'll need to download some files to follow this workshop:
~~~
git config --global http.sslverify false
git clone https://git.ecdf.ed.ac.uk/sllabres/files_ambertools4cp2k.git
~~~
{: .language-bash}


> ## About the data
>
> It provides:
> * PDB file  with the initial coordinates. For this workshop we will use a X-ray structure of the main protease of the SARS-CoV2 virus (PDB id [5R84](https://www.rcsb.org/structure/5R84)) with a small-molecule bound to its catalytic site.
> * AmberTools input files (LEap, sander and cpptraj input files)
> * Topology, coordinates and trajectory files. 
>
{: .prereq}

Once you have downloaded the files you can go into the `files_ambertools4cp2k` folder and proceed to the next section:

~~~
cd files_ambertools4cp2k
~~~
{: .language-bash}

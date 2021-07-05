---
title: Environment set up and downloading files 
teaching: 5
exercises: 0
questions:
- "Which software will be used in this workshop?"
objectives:
- "Download necessary data for the workshop"
keypoints:
- "We need several pieces of software to model biological systems"
---


In this session we are going to use several software packages:  

- [AmberTools](https://ambermd.org/AmberTools.php) (We will use the latest version of Ambertools. If you are using an older version, commands might run slightly different.)
- [PROPKA](https://github.com/jensengroup/propka-3.1) or [PROPKA server](http://server.poissonboltzmann.org/pdb2pqr) 
- [OpenBabel](http://openbabel.org/wiki/Main_Page)
- [Pymol](https://sourceforge.net/projects/pymol/) or [VMD](https://www.ks.uiuc.edu/Research/vmd/) or another molecular visualisation tool of your preference.
- [Marvin Sketch](https://chemaxon.com/products/marvin).

***

You should check that both `pymol`and `babel`are installed in your workstation. Try to type `pymol` anb `babel` respectively. If they are not installed you should download them using the following command:

~~~
apt-get install pymol
apt-get install babel
~~~
{: .language-bash}

If you have anyy trouble, let us know. 

***

## Downloading data

You'll need to download some files to follow this workshop, since there is no `git` installed. You should visit [this page](https://github.com/salomellabres/files_MDbasictutorial) and download the files.

> ## About the data
>
> It provides:
> * PDB file  with the initial coordinates. For this workshop we will use a X-ray structure of the main protease of the SARS-CoV2 virus (PDB id [5R84](https://www.rcsb.org/structure/5R84)) with a small-molecule bound to its catalytic site.
> * AmberTools input files (LEap, sander and cpptraj input files)
> * Topology, coordinates and trajectory files. 
>
{: .prereq}

Once you have downloaded the files you can go into the `files_MDbasictutorial` folder and proceed to the next section:

~~~
cd files_MDbasictutorial
~~~
{: .language-bash}

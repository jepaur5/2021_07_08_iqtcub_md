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



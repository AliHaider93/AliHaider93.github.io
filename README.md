 **BRL-CAD GSoC project**
![Car](https://github.com/AliHaider93/AliHaider93.github.io/blob/main/Car.png){500x500}
# GSoC project report: 
This page contains a report of my GSoC project "Implementation of a g-k converter and Improvements to the existing k-g converter" with a summury of the project and its outcomes. 
## Project summary: 
The goal of the project is to creat a converter from LS-DYNA .k files to BRL-CAD .g files from now on we will call it (k-g) converter , and vice versa. LS-DYNA is an advanced general-purpose multiphysics simulation software package and BRL-CAD is a constructive solid geometry (CSG) solid modeling computer-aided design (CAD) system. The benefits of creating this converter is clear from both sides, where converting a .k file to BRL-CAD data base allowes the user to benfite from the vast cababilities of BRL-CAD, like modifying the geometry and performing a render using the advanced Raytracing cababilites of BRL-CAD. On the other side the conversion of a model from BRL-CAD to LS-DYNA allows the users to run a physical/ structural analysis using a geometry on which they invested a non-trivial amount of time creating in BRL-CAD. A prliminary implimintaion of the (k-g) converter was created by the BRL-CAD comunity <[deve_k-g](https://github.com/BRL-CAD/brlcad/tree/devel_k-g) and my contribution was concentrated on the implementation of additional fetures to the existing converter. Here I will present a list of outcomes.
1. [] Bug Fixing




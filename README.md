 **BRL-CAD GSoC project**

# GSoC project report: 
This page contains a report of my GSoC project "Implementation of a g-k converter and Improvements to the existing k-g converter" with a summury of the project and its outcomes. 
## Project summary: 
The goal of the project is to creat a converter from LS-DYNA .k files to BRL-CAD .g files from now on we will call it (k-g) converter , and vice versa. LS-DYNA is an advanced general-purpose multiphysics simulation software package and BRL-CAD is a constructive solid geometry (CSG) solid modeling computer-aided design (CAD) system. The benefits of creating this converter is clear from both sides, where converting a .k file to BRL-CAD data base allowes the user to benfite from the vast cababilities of BRL-CAD, like modifying the geometry and performing a render using the advanced Raytracing cababilites of BRL-CAD. On the other side the conversion of a model from BRL-CAD to LS-DYNA allows the users to run a physical/ structural analysis using a geometry on which they invested a non-trivial amount of time creating in BRL-CAD. A prliminary implimintaion of the (k-g) converter was created by the BRL-CAD comunity <[deve_k-g](https://github.com/BRL-CAD/brlcad/tree/devel_k-g) and my contribution was concentrated on the implementation of additional fetures to the existing converter. Here I will present a list of outcomes.
### Bugs:

#### Empty Database Error:
In the old (k-g) converter there was an issue with converting a .k file related to ifstream::tellg() returning a wrong position in the ASCII file. The solution was to open the input file (.k) as binary file. The pull request for this bug fix is provided in this link <[patch](https://github.com/BRL-CAD/brlcad/pull/118).

#### LS-DYNA Part Made out only of Triangles: 
an old piece of code was left in the new k-g converter. in this code when a the converter encounters a trianlge, it tries to go back to the privous part. This code was erronous considering that an LS-DYNA part can contain shell elements which could be quadrilaterals or Triagles or any other available type of shell elements. Another issue was that if the converter is in the first part and it tries to go back to the privous part it will find nothing and it will attempt to read a piece of memory which is not allocated.
The fix is provided in the following link <[update k-g.cpp](https://github.com/BRL-CAD/brlcad/pull/134)

### Developments:

#### Handling LS-DYNA command *PART_ADAPTIVE_FAILURE:
In LS-DYNA a model is devided into parts, these parts contain information about finite elements and there properties. The LS-DYNA part is connected to a certain mesh, and this mesh can be changed during the analysis. This feature is implemented in LS-DYNA through the command *PART_ADAPTIVE_FAILUR. Considering the importance of this information related to adaptive meshing of a part at a certain point in time. we have implemented this command as an attribute for the reagion where the part exists. This implementation is of interest in case one wants to convert the .g file file back to a .k file and it allowes the perservation of this important information. The pull request related to this development can be found in the following link <[*PART_ADAPTIVE_FAILURE](https://github.com/BRL-CAD/brlcad/pull/142).
The following image showes how one can check for such attribute in a region on MGED
![attributes](https://github.com/AliHaider93/AliHaider93.github.io/blob/main/attr.png){617x272}

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
<img src="/attr.png" width="500" height="500">
#### Reading Format: 
There are mutliple formats for a .k file, the first issue was encountered reading nodes coordinates in a .k file. The initial assumption was to consider space separated values. This was a wrong assumption considering that a .k file has three types of format fixed format (comma separated), free format ( known number of characters for each field) which include standard and long formats, and I10 format. an implementation of the node standard format can be found in the following link <[node standard format](https://github.com/BRL-CAD/brlcad/pull/143). the comma separated format, and the I10 format implementation can be found in the follwoing link <[formats](https://github.com/BRL-CAD/brlcad/pull/144). The format of a .k file can be set on the begining of the file after the command *KEYWORD for example if a file has the long format one will find one of the following commands (*KEYWORD LONG=K) (*KEYWORD LONG=Y) (*KEYWORD LONG=S), while in case of I10 format the command is (*KEYWORD I10 LONG=S), nontheless a node format can be specified later on in a file. In case node format is I10 the *NODE keyword will be followed by the symbole (%). Additionaly this pull request handles the long format adabted in old .k files, where a node with Long format is spread on two lines.










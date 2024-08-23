 **BRL-CAD GSoC project**

# GSoC project report: 
This page contains a report on my GSoC project "Implementation of a g-k converter and Improvements to the existing k-g converter" with a summary of the project and its outcomes. 
## Project summary: 
The project's goal is to create a converter from LS-DYNA .k files to BRL-CAD .g files from now on we will call it (k-g) converter, and vice versa. LS-DYNA is an advanced general-purpose multiphysics simulation software package and BRL-CAD is a constructive solid geometry (CSG) solid modeling computer-aided design (CAD) system. The benefits of creating this converter are clear from both sides, where converting a .k file to a BRL-CAD database allows the user to benefit from the vast capabilities of BRL-CAD, like modifying the geometry and performing a render using the advanced Raytracing capabilities of BRL-CAD. On the other hand, converting a model from BRL-CAD to LS-DYNA allows the users to run a physical/ structural analysis using geometry, for which they invested a non-trivial amount of time creating in BRL-CAD. A preliminary implementation of the (k-g) converter was created by the BRL-CAD community <[deve_k-g](https://github.com/BRL-CAD/brlcad/tree/devel_k-g) and my contribution was concentrated on the implementation of additional features to the existing converter. Here I will present a list of outcomes.
### Bugs:

#### Empty Database Error:
In the old (k-g) converter there was an issue with converting a .k file related to ifstream::tellg() returning a wrong position in the ASCII file. The solution was to open the input file (.k) as a binary file. The pull request for this bug fix is provided in this link <[patch](https://github.com/BRL-CAD/brlcad/pull/118).

#### LS-DYNA Part Made out only of Triangles: 
an old piece of code was left in the new k-g converter. in this code when the converter encounters a triangle, it tries to go back to the privous part. This code was erroneous considering that an LS-DYNA part can contain shell elements which could be quadrilaterals Triangles or any other available type of shell elements. Another issue was that if the converter is in the first part and it tries to go back to the privous part it will find nothing and it will attempt to read a piece of memory that is not allocated.
The fix is provided in the following link <[update k-g.cpp](https://github.com/BRL-CAD/brlcad/pull/134)

### Developments:

#### Handling LS-DYNA command *PART_ADAPTIVE_FAILURE:
In LS-DYNA a model is divided into parts, these parts contain information about finite elements and their properties. The LS-DYNA part is connected to a certain mesh, and this mesh can be changed during the analysis. This feature is implemented in LS-DYNA through the command \*PART_ADAPTIVE_FAILUR. Considering the importance of this information related to the adaptive meshing of a part at a certain point in time. we have implemented this command as an attribute for the region where the part exists. This implementation is of interest in case one wants to convert the .g file back to a .k file and it allows the preservation of this important information. The pull request related to this development can be found in the following link <[PART_ADAPTIVE_FAILURE](https://github.com/BRL-CAD/brlcad/pull/142).
The following image shows how one can check for such attributes using MGED
<img src="/attr1.png" width="1000" height="300">
*Attributes in a region*


#### Reading Format: 
There are multiple formats for a .k file, the first issue was encountered reading node coordinates in a .k file. The initial assumption was to consider space-separated values. This was a wrong assumption considering that a .k file has three types of format fixed format (comma separated), free format ( known number of characters for each field) which includes standard and long formats, and I10 format. an implementation of the node standard format can be found in the following link <[node standard format](https://github.com/BRL-CAD/brlcad/pull/143). the comma separated format, and the I10 format implementation can be found in the follwoing link <[formats](https://github.com/BRL-CAD/brlcad/pull/144). The format of a .k file can be set at the beginning of the file after the command *KEYWORD for example if a file has the long format one will find one of the following commands (\*KEYWORD LONG=K) (\*KEYWORD LONG=Y) (\*KEYWORD LONG=S), while in case of I10 format the command is (\*KEYWORD I10 LONG=S), nonetheless a node format can be specified later on in a file. In case the node format is I10 the \*NODE keyword will be followed by the symbol (%) i.e (\*NODE %), while the command(\*NODE +) indicates the long format, and (\*NODE -) indicates the standard format. Additionally, this pull request handles the long format adapted in old .k files, where a node with a Long format is spread on two lines.
#### Case-insensitive keywords
The keywords in the .k file are case insensitive, hence we have included a code to handle this case. the code can be found in the following link <[insensitve](https://github.com/BRL-CAD/brlcad/pull/146). later on, it was noted by my supervisor that this situation could be handled in a simpler way <[oneLine](https://github.com/BRL-CAD/brlcad/pull/150)
#### Solid Elements and Geometry Class: 
The elementary implementation of the k-g converter was able to handle shell elements. I have worked on developing the Solid element. Initially, the elements had to be read from the .k file then the information should be stored in an intermediate data structure, for which I have developed the Arbs class. This pull request was adapted to handle different kinds of solid elements, the traditional Hex8 element was implemented as arb8 on the BRL-CAD side. other elements are handled with arb7 for 7 nodes solid element, arb6 for six nodes solid element, arb5 for 5 nodes solid element, and arb4 is still under development. Additionally, the Geometry class was developed to include the Bot class and the Arbs class. This development allowed the conversion of any model that contains solid elements. as shown in the following image 
<img src="/beam.png" width="879" height="494">
*Solid elements converted from .k file to BRL-CAD database*
#### Random coloring of the parts: 
Considering that each part in a .k file is represented as a region in a .g file. If all the regions had the same color the resulting converted model is hard to see clearly. To avoid this issue random coloring was implemented for each region. In the following images, one can see the difference between a model with only one color and a model after the implementation of the random coloring. The code can be found in <[coloring](https://github.com/BRL-CAD/brlcad/pull/153)

<img src="/car1color.png" width="1000" height="500">
*Car model with one color*

s
<img src="/carRandColor.png" width="1000" height="500">
*Car model with random coloring*

#### Systematic Implementation of Commands and Options: 
Considering the huge amount of commands and their corresponding options in LS-DYNA, an approach that relies on converting example .k files and fixing the error is bound to be inefficient, hence I have started to implement all available commands and their corresponding options in a systematic way relying on LS-DYNA documentations. The following pull request <[commands and options](https://github.com/BRL-CAD/brlcad/pull/151) has been opened to handle the following issues: 
+ Distinction between a command and its options
+ Implementation of beam element and its options on the parser's side. Here is a list of the options:
  +  Option Thickness
  +  Option Scalar
  +  Option Section
  +  Option Pid
  +  Option Offset
  +  Option Orientation
  +  Option Warpage
  +  Option Elbow
+ Implementation of pulley element on the parser's side
+ Implementation of beam source element on the parser's side
+ Implementation of the bearing element on the parser's side
+ Implementation of the \*SECTION command on the parser's side. Here is a list of the available sections
  + Section_Ale1d,
  + Section_Ale2d,
  + Section_Beam,
  + Section_Beam_AISC,
  + Section_Discrete,
  + Section_Fpd,
  + Section_Point_Source,
  + Section_Point_source_Mixture,
  + Section_Seatbelt,
  + Section_Shell,
  + Section_Solid,
  + Section_Solid_Peri,
  + Section_Sph,
  + Section_Tshell
+ Implementation of a beam with cross-section type 1 which is a tubular cross-section on the BRL-CAD side. This beam element is represented as a pipe primitive in the BRL-CAD database.  The implementation of the pipe class is also included in this pull request.

#### Implementation of the Resultant beam:
A beam in LS-DYNA can have diffrent kinds of cross section, in order to represent these kinds of beams in the .g database, I am working on developpin the sketch and extrude classes. The goal is to implement beam elements with the following types of cross-sections: 

+ SECTION_01 : I - Shape
+ SECTION_02 : Channel
+ SECTION_03 : L - Shape
+ SECTION_04 : T - Shape
+ SECTION_05 : Tubular box
+ SECTION_06 : Z - Shape
+ SECTION_07 : Trapezoidal
+ SECTION_08 : Circular
+ SECTION_09 : Tubular
+ SECTION_10 : I - Shape 2
+ SECTION_11 : Solid box
+ SECTION_12 : Cross
+ SECTION_13 : H - Shape
+ SECTION_14 : T - Shape 2
+ SECTION_15 : I - Shape 3
+ SECTION_16 : Channel 2
+ SECTION_17 : Channel 3
+ SECTION_18 : T - Shape 3
+ SECTION_19 : Box - Shape 2
+ SECTION_20 : Hexagon
+ SECTION_21 : Hat - Shape
+ SECTION_22 : Hat - Shape 2

Currently the  beam with SECTION_01 : I - Shape is in under development. The following immage shoes the sketch of the cross section in .g database 

<img src="/IBeam.png" width="761" height="829">
*SECTION_01 : I - Shape*

## TO DO:
The project is not finished by anymeans there are still a substantial amount of work to do. 
Here is a preliminary to do list:
+
+
+
+

## Useful information:
+ Link to my daily blog during the coding period <[blog](https://brlcad.org/wiki/Ali_Haydar_Dev_log_2024#Coding_period)
+ Link to my github account <[github](https://github.com/AliHaider93)

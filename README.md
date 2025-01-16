# Wind-Distribution-In-Historic-District-With-OpfenFOAM

This is yet another example of what FOSS software is capable of in 2025. The whole model below consists of 33 buildings plus roofs (including one church) and has been prepared in FreeCAD. 
Albeit the models are quite basic, they should be enough for such a large scale simulation. The town block has been prepared after basic Google Maps data of the inner town area of Graz, Austria. The ground plots of all buildings are accurate.

As this repository is for demonstration purposes only, the roofs are not realistic. The actual roofs of this area may be called squiggled at best. They are the result of more than one hundred years of repairing and maintenance and two World Wars. 
The model space is roughly 400 * 200 * 43 m (L x W x H). These data is exported to Salome for creating the simulation space and a combined .step of the town block. FreeCAD can do that, but it takes a long time with over a hundred bodies.
The simulation space data is reimported to FreeCAD for use with the CfDOF Workbench. If you have ever struggeed with OpenFOAM and the basic text editing of files (I certainly have in he past), this is perhaps the best solution if you do not have 50000€ for a CFD License of the usual suspects. 

Piece of map used for the model (courtesy Google Maps):
![Bildschirmfoto vom 2025-01-15 13-07-30](https://github.com/user-attachments/assets/90ede3e4-da1e-4eab-bf79-22694629eb63)

CfdOF allows to do basically every step of the CFD simulation. All walls in the model, apart from the left, right and top of the space, are noslip walls. The mentioned three walls of the simulation space are slip walls. Inlet speed is 10m/s (36km/h) which is already a strong breeze. The direction of wind flow is from the Mur river eastwards, at a slight angle along Murgasse and approx. perpendicular to Neutorgasse. 
For meshing the town we use SnappyHexMesh. Personally, I could never get the meshing right in SnappyHexMEsh before using CfdOF, also the naming of geometry groups is passed on to the mesh automatically, so we can surely call this an 'integrated' solution for OpenFOAM. In this setup, SnappyHexMesh can also be called with the -parallel option to reduce meshing time considerably. The number of cells is around 10.5M on 24 cores this took 30 minutes (0.7m basic element size with refinement 0.5, basic boundary layer elements n=3, minThickness 0.1). Mesh checking etc. is also done in an automated fashion. Personally, I haven't had any major problems with it, the created castellated mesh is ready to go. Applying boundary layer cells is a bit trickier, in a mesh this size, there will likely be always a few regions with inferior cells. Of course, mesh refinements may also be applied. Just like with the file preparation for OF, you can always jump into editing the created text files and when creating boundary layers, manual editing is absolutely necessary. Just be sure to define the boundaries before moving over to meshing, this is a common mistake in OF. 

![Bildschirmfoto vom 2025-01-16 12-52-50](https://github.com/user-attachments/assets/090ee79b-ebb5-46fa-83ff-b08b5c0d3524)

The CfdSolver part is also very convenient, although only the most important solvers are implemented (just to remind the reader: OF has nearly 50 solvers!). If you need reactingFoam or other exotic solvers, you still need to modify files by hand. Let's hope more solvers will be included in future versions. Also here, we can use MPI for parallel execution of our simulation. For this demonstration, pimpleFoam (transient solver) was used. We calculate laminar, isothermal flow. After entering the appropriate values for endTime, timeStep etc. and starting, we are presented a graph with residuals, very nice! In this case, the convergence is quite smooth, as everything is laminar and well-behaved. Also very helpful is a clock that shows current running time of the simulation, it is also presented during meshing. Everything is written into one single log file, which is shown below the window. Logs can always be stored separately, the window can be cleared from old logs. At t=1s we had 1000 iterations which took 2h22minutes on 24 cores.

![Bildschirmfoto vom 2025-01-16 12-50-17](https://github.com/user-attachments/assets/67e37921-1582-4128-a5e9-adb77303025d)

For post-processing, the well-known ParaView is used. Today we can safely call it 'the post-processing standard' in the open-source world. As it is also used in Salome-Meca/Code-Aster we are very familiar with it, so now news here. ParaView can already be started during the running simulation, as long as at least one timestep is written. You can leave it open and just press 'Reload files (F5)' and follow the results of your running simulation. In Paraview, we may also load a pretty model of our town block for visualisation, just be sure to store it in .vtk before importing it (Salome can do that). 
Let us look at some results. When looking at streamlines, a line source is very useful in this setup. We place it at the left border, near the inlet in different heights (1.0m, 1.5m, 2.0m). It is very obvious the first corners of Murgasse catch a lot of wind, and windspeed even increases there. In fact, these corners are known to be quite windy, as is Franziskanerplatz. On the main spare (Hauptplatz) we find the streams that were near the ground on the inlet to pass over buildings and fall onto the square with reduced velocity. Near the main square, we find two narrow alleys, that are known to be windy in reality, Franziskanergasse and Neue-Welt-Gasse. The results of this first (!) simulation confirm reality, as the streamlines coming from the river can pass at nearly unhindered at the same height. There is not a lot of disturbance for the air along these paths. 

Top view of whole simulation space with line source on the left (height 1.5m):

![top_view_1](https://github.com/user-attachments/assets/c15c0625-88d6-4041-8b31-11f775323772)

Angled view at the church and the corner at Murgasse (same line source in height of 1.5m):

![side_view_church_1](https://github.com/user-attachments/assets/04419415-b278-4f41-9a0f-03882a88af0f)

Increased wind speeds are present at the corner of Murgasse, with indicated line source in height of 1.5m. Of course, 19th century city planners did not consider aerodynamics of the town. For the aerodynamicist, it's clear why we find increased wind speeds at the entrance of Murgasse and further back. In both cases the entrance cross section areas are decreased and speeds increase. Further back there is a second constriction at the corner of Paradeisgasse, some air even escapes through Nürnbergergasse, which is it's own little wind tunnel:

![murgassse_nürnberger_1](https://github.com/user-attachments/assets/c727514f-91ed-44e0-8d39-8a1537427b49)

Increased wind speeds can be found at the corner to Franziskanergasse also, our second 'pedestrian wind tunnel' in the inner district. At the corner a little swirl develops, be sure to have your combs ready after passing it:

![franziskanergasse_1](https://github.com/user-attachments/assets/b29a7b87-e4b4-42eb-97ec-3176b23a409e)

In summary, using CfdOF is a real pleasure compared to other open-source GUIs for OpenFOAM. The GUI is 100% FreeCAD oriented, so if you are familiar with it you will have no problem adapting. The program never crashes or fails in unforeseen or stupid ways, it seems to be very stable even with large meshes. Compared to other FOSS GUI, this is a real pleasure. 

The basic workflow used here was:

CAD model (FreeCAD)

Simulation space and other CAD cutting and concatenating, measurements (Salome)

Meshing (SnappyHexMesh parallel in CfdOF in FreeCAD)

Simulation (OpenFoam parallel in CfdOF in FreeCAD)

Post-Processing (ParaView in CfdOF in FreeCAD).


Although this text sounds like an ad, we are in no way affiliated with the creators of Cfd. Personally, I have tried OpenFOAM many times and put it away after a few days, exactly as many times. CfdOF has changed that, I'd even say it is mature enough to be used professionally. The CAD model used in the simulation is shared in the folder, just be informed it is not super-accurate and in no way usable for an architectural model of the town. 


emefff@gmx.at


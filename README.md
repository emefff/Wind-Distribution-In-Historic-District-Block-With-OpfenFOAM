# Wind-Distribution-In-Historic-District-Block-With-OpfenFOAM

This is yet another example of what FOSS software is capable of in 2025. The whole model below, consisting of 33 buildings plus roofs (including one church) has been prepared in FreeCAD. 
Albeit, the models are quite basic, they should be enough for such a large scale simulation. The city block has been prepared after basic Google Maps data of the inner city area of Graz, Austria. The ground plot of all buildings is accurate.

As this repository is for demonstration purposes only, the roofs are not realistic. The actual roofs of this area may be called squiggled at best. They are the result of more than one hundred years of repairing and maintenance and two World Wars. 
The model space is roughly 400 * 200 * 43 m (L*W*H). These data is exported to Salome for creating the simulation space and a combined .step of the city block. FreeCAD can do that, but it takes a long time with over a hundred bodies.
The simulation space data is reimported to FreeCAD for use with the CfDOF Workbench. If you have ever struggeed with OpenFOAM and the basic text editing of files (I certainly have in he past), this is perhaps the best solution if you do not have 50000€ for a CFD License of the usual suspects. 
CfdOF allows to do basically every step of the CFD simulation. All walls in the model, apart from the left, right and top of the space, are noslip walls. The mentioned three walls of the simulation space are slip walls. Inlet speed is 10m/s (36km/h) which is already a strong breeze. The direction of wind flow is from the Mur river eastwards, at a slight angle along Murgasse and approx. perpendicular to Neutorgasse. 
For meshing the city we use SnappyHexMesh. Personally, I could never get the meshing right in SnappyHexMEsh before using CfdOF, also the naming of geometry groups is passed on to the mesh automatically, so we can surely call this an 'integrated' solution for OpenFOAM. In this setup, SnappyHExMesh can also be called with the -parallel option to reduce meshing time considerably. The number of cells is around 8.7M on 16 cores this took 20 minutes (0.7m basic element size with refinement 0.5). Mesh checking etc. is also done in an automated fashion. Personally, I haven't had any major problems with it, the created mesh is ready to go. Of course, mesh refinements may also be applied. Just like with the file preparation for OF, you can always jump into editing the created text files, which you remember from using OF with text input from before. Just be sure to define the boundaries before moving over to meshing, this is a common mistake in OF. 

![Bildschirmfoto vom 2025-01-15 10-46-29](https://github.com/user-attachments/assets/ebce6b8f-2bf7-4604-bcd7-f9dd7867ecd7)

The Cfd-solver part is also very convenient, although not all solvers are implemented. If you need reactingFoam or other exotic solvers, you still need to modify files by hand. Let's hope more solvers will be included in future versions. Also here, we can use MPI for parallel execution of our simulation. For this demonstration, pimpleFoam (transient solver) was used. We calculate laminar, isothermal flow. After entering the appropriate values for endTime, timeStep etc. we are presented  a graph with residuals, very nice! In this case, the convergence is quite smooth, as everything is laminar and well-behaved. Also very helpful is a clock that shows current running time of the simulation, which is also presented during meshing. Everything is written into one single log file, which is shown below the window. Logs can always be stored separately, the window can be cleared from old logs. 250 iterations (tStep = 1E-3) took ~25m minutes on 24 cores. At t=1s we had 1000 iterations which took 1h25minutes on 24 cores.



For post-processing, the well-known ParaView is used. Today we can safely call it 'the post-processing standard' in the open-source world. As it is also used in Salome-Meca/Code-Aster we are very familiar with it, so now news here. ParaView can already be started during the running simulation, as long as at least one timestep is written. You can leave it open and just press 'Reload data' and follow the results of your running sim. In Paraview, we may also load a pretty model of our town block for visualisation, just be sure to store it in .vtk before importing it (Salome can do that). 


Let us look at some results. When looking at streamlines, a line source is very useful in this setup. We place it at the left border, near the inlet in different heights. It is very obvious the first corners of Murgasse catch a lot of wind, and windspeed even increases there. In fact, these corners are known wo be quite windy as is Franziskanerplatz. On the main spare (Hauptplatz) we find the streams that were near the ground on the inlet to pass over building and fall onto the square. Near the main square, we find two narrow alleys, that are known to be windy in reality, Franziskanergasse and Neue-Welt-Gasse. The results of this simulation confirm reality, as the streamlines coming from the river can pass at nearly unhindered at the same height. There is not a lot of disturbance for the air along these paths. 


In summary, using CfdOF is a real pleasure compared to other open-source GUIs for OpenFOAM. The GUI is very FreeCAD oriented, so if you are familiar with that you will have no problem adapting. The program never crashes or fails in unforeseen or stupid ways, it seems to be very stable even with large meshes. Compared to other FOSS GUI, this is a real pleasure. 


The basic workflow used here was:

CAD model (FreeCAD)

Simulation space and other CAD (Salome)

Meshing (SnappyHExMesh in CfdOF in FreeCAD)

Simulation (OpenFoam in CfdOF in FreeCAD)

Post-Processing (ParaView in CfdOF in FreeCAD).





Although this sounds like an ad, I am in no way affiliated with the creators of Cfd, Personally, I have tried OpenFOAM many times and put it away after a few days exactly as many times. CfdOF has changed that, I'd even say it is mature enough to be used professionally. The CAD model used in the simulation is shared in the folder, just be informed it is not super-accurate and in no way usable for an architectural model of the city. 


emefff@gmx.at


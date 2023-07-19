## Project Design Cycle

The stages in creating and running a project are as follows:

-  Create .edl and .elf files that make up the project description and entity icons. This allows the design to be visualised on-screen as a static image. Errors in these files will be reported in the Output panel; some errors in the .elf file will be apparent from the image.

-  Write Hase++ behavioural code for each entity (*entity.hase* files) and, if appropriate, global\_fns.h and global\_fns.cpp file. global\_fns.cpp must have #include "XXXX.h" in line 1 where XXXX is the name of the project in the .edl file.

-  Go to the Project Menu and use Build Project to compile the project.  The first thing HASE does is create C++ (.cpp) files from the .hase files. It then compiles the .cpp files, in the order in which entities appear in the .edl file, to create the .o files. If the compilation fails for one file, those following will not be compiled, so there won't be a .o file.  When there are compilation errors, examine the relevant *entity.cpp* file to find where the errors are, correct them in the *entity.hase* file and re-compile.

-  Once the code has compiled correctly, create the simulation inputs to the arrays as *.mem* files, *e.g.* the initial contents of the programmable registers in the Example Project (declared in the s-comp.edl file as <tt>RARRAY (prog\_reg\_set, main\_reg, 16)</tt> in the proc (processor) entity) are held in PROC.main\_reg.mem. HASE automatically copies contents of *.mem* files into the relevant arrays.

-  Use Simulate in the Project Menu (or the Run icon) to run the simulation. It probably won't run correctly first tim. It's often helpful to put print statements (*e.g.* "printf("SCOREBOARD&nbsp;getting&nbsp;request&nbsp;release&nbsp;from&nbsp;unit&nbsp;%d\n",&nbsp;unit\_no)") in the entity code to see what is happening (or, more usually, to find out what is not happening).

As the simulation runs, it generates a tracefile. The default tracefile file name is <*project\_name*>.sim, stored in a Results subdirectory. If you want to keep multiple tracefiles, you can copy the created file to another name in the same directory.

-  Use Load Trace File in the Project Menu (or the Clock icon) to load the tracefile. This will invoke a pop-up selector box.  Once the tracefile is loaded the animator control buttons in the Project View panel are activated. It's often helpful to single shot the playback and observe carefully what is happening, especially when the simulation appears not to be correct.

-  Some useful tips:

  -  Building a project creates an XXXX.params file. If you alter the .edl file after this and re-load the project, it may produce lots of .edl errors. Deleting the XXXX.params file and then re-loading again usually fixes this problem.
  -  The "Simulation finished with errors" problem is usually caused by accesses to undefined memory locations. This can happen if an <tt>RARRAY</tt> has not been initialised properly (*e.g.* by the appropriate .mem file) or if there's an error in    cross-references between entities, e.g. in a statement such as "myClock&nbsp;=&nbsp;(Clock*)&nbsp;sim.get\_entity(sim.get\_entity\_id("CLOCK"))", so check these very carefully. Otherwise, try inserting a stopSimulation() command in the code to stop the simulation early and keep moving the stopping point to pinpoint which simulated instruction is causing the error.
  -  If a simulation runs to completion but doesn't appear to work correctly (*e.g.* when watching the playback), then it can sometimes help (a) to read the trace file to look for something that doesn't seem right and/or (b) insert print statements as noted above.

[<- Userguide](<Userguide.md>)
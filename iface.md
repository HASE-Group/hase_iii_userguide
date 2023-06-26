## The User Interface

The HASE III window contains a Menu Bar at the top, a Project View panel, a Project Inspector panel, an Output panel (the HASE Console) and a status line at the bottom.

### Menu Bar

The Menu Bar has four pull-down menus (File, View, Project and Tools) and individual icons corresponding to the various menu items.

### Tool Bar

| Image | Name | Action|
|:-------|:-----------|:-------------------|
|<img  src="images/proj-butt.gif" alt="project button">|Open Project|Pops up a project selector box|
|<img src="images/file-butt.gif" alt="file button">|Open File|Pops up a file selector box; the selected file is displayed in the project view panel|
|<img  src="images/reload-butt.gif" alt="reload button">|Reload Project| |
|<img  src="images/save.gif" alt="save button">|Save File|Saves file - only active after an open file has been editted|
|<img  src="images/save-all.gif" alt="save all button">|Save All Files|Saves all files - only active after an open file has been editted|
|<img  src="images/build-butt.gif" alt="build button">|Build Project|Creates the project simulation executable|
|<img  src="images/clean-build-butt.gif" alt="clean-build button">|Clean & Build Project|Deletes created files and rebuilds the project simulation executable|
|<img  src="images/props-butt.gif" alt="properties button">|Project Properties|Opens a project properties display window|
|<img  src="images/edit-param-butt.gif" alt="edit parameters button">|Edit Parameters|Re-enables parameter editing after a trace file has been loaded|
|<img  src="images/write-param-butt.gif" alt="write parameters button">|Write Parameters|Updates the <i>project</i>.params file with changes made in the GUI to the values of parameters declared in the project definition files|
|<img  src="images/deleteparams.png" alt="delete parameters button">|Delete Parameters|Deletes the project's .params file, resetting parameter values back to the default value defined in the project's EDL file|
|<img  src="images/run-butt.gif" alt="run button">|Run Simulation|Runs the simulation|
|<img  src="images/trace-butt.gif" alt="trace button">|Load Trace File|Pops up a trace file selector box|

### Project View panel

This is the main panel in the window, showing the project entities and their interconnections. This panel is also used to display files opened using the File Selector icon in the Tool Bar.

The Project View panel also contains the animation controls. These are activated once a trace file has been loaded.

### Project Inspector panel

When a project is loaded, this panel gives structured access to the data in the .edl file defining the project. The menu bar at the top of the panel allows *Parameters* to be selected instead. This dis\
plays the current state of all the parameters of the project.  When a simulation is being played back during animation, the parameter values are simultaneously updated in this panel.

The parameter display for each entity can be dragged out from the parameters panel into a separate window either by grabbing the speckled area using the left mouse button or by left clicking on its ico\
n whilst holding down the shift key (the latter facility is currently unavailable on MACs).

### Output panel

This is the HASE Console window, *i.e.* it acts as a terminal window displaying information about the progress of a compilation or simulation run. The outputs from any print statements included in the \
.hase files will be displayed here.

### Status Line

The Status Line displays status information informing the user of the current operating state of HASE.

[<- Userguide](<https://github.com/HASE-Group/Documents/blob/main/Userguide.md>)

## GLOBALS - Global Parameter Library

### Synopsis

The **GLOBALS** section of the project definition file contains definitions of <a href="parameters.html">parameters</a> that are accessible to all of the entities in the project.


#### HASE++: Reporting global parameter changes

In the same way that changes of state are only recorded in the tracefile when a subsequent <a href="hasepp.html"><tt>dump_state()</tt></a> statement is executed, changes to values of global parameters that occur during a simulation are only recorded in the tracefile when a subsequent
<tt>dump\_globals()</tt> statement is executed.

### HASE++: Shared access to global parameters

Global parameters can be accessed from the body of any entity, therefore extra care must be take to ensure that access is appropriately synchronized. HASE creates a mutual exclusion object for each global parameter with a name matching the original parameter name but affixed with _mutex. This object has two functions: Lock() and Unlock(). While inside a locked region of access (between Lock() and Unlock()) no other threads will be able to enter a region of code surrounded with a Lock() and Unlock() called on the same global parameter.

Below is a snippet of code from the body of an entity that may be invoked by several instances of that entity (executing as separate threads simultaneously).  The .Lock() and .Unlock() calls ensure that the modification to the global parameter *nodes_done* is synchronised between the instances of the entity. dump\_globals() is called after modification to record the change to *nodes\_done* 
in the simulation tracefile.

<pre>
&nbsp;&nbsp;&nbsp;&nbsp;nodes_done_mutex.Lock();
&nbsp;&nbsp;&nbsp;&nbsp;nodes_done++;
&nbsp;&nbsp;&nbsp;&nbsp;dump_globals();
&nbsp;&nbsp;&nbsp;&nbsp;nodes_done_mutex.Unlock();
</pre>

[<- Defining a Project](<https://github.com/HASE-Group/Documents/blob/main/proj\
ect.md>)

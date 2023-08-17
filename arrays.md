## Initialising Arrays

Arrays are instantiated in entities by a declaration of the form:

<tt>RARRAY ( type\_name, instance\_name, no\_of\_elements)</tt>

where:

- **type\_name:** *str* is the name of the [ARRAY](<parameters.md#rarray>) type, declared in the PARAMLIB section of the project definition.

- **no\_of\_elements:** *str or int (-1 or >0)* is the number of elements in the array. Several mechanisms are provided for specifying this number but in every case the size of the array has to be known before the simulation begins.

### no\_of\_elements:

#### int (>0) 

The integer value which is specified is the default size. That is to say: for each RARRAY created from this type, an array of the size specified in that field will be allocated at the beginning of the simulation.  The HASE user interface allows users to modify the size of RARRAY parameters. Each instance of a defined ARRAY is independent regarding its number of elements.

#### int -1

-1 is to be set (before building the project: in EDL or with the GUI) whenever the user wants the size of the array to be computed from values of other parameters.

- If -1 is set before building the project, a more complex C++ code is generated.

- If -1 was set before building and is unset before running the simulation, the complex code is simply not used and the new integer
value (>0) is used to set the size of the array.

- If -1 was not set before building and is set just before running the simulation an error will occur since the complex code would have not been generated (Just re-build the project in that case).

- If -1 was set before building and is still set before running the simulation, a pre-declared method is called to compute the size of the array. The user MUST define this method in the .hase file of each entity which declares an RARRAY with the -1 size. This method is called **typename\_f()**, has no parameter and must return an int.

#### str

The user may also type the name of an **RINT** parameter (See the [reference parameters](<parameters.md>) section) to represent the number of elements.  The value taken by this parameter just before running the simulation will be used to set the size of the array.  This value MUST be positive. The RINT parameter can either be declared in the [GLOBALS](<globals.md>) section of the EDL file or as a parameter of the entity which defines the RARRAY.

#### Examples

In project.edl:

```

1. ARRAY ( Memory, Memory\_Contents\_Structure )
2. ARRAY ( Cache, int)
...
ENTITY Sender (
...
	PARAMS (
			RINT(no\_lines, 256)
 			RINT(no\_blocks, 4)
			RARRAY(Cache, data, **-1**)
...
```

In Sender.hase:

```
$class\_defs

int Sender::data\_size**\_f**() { return no\_lines*no\_blocks;}
```

<h3>Initialisation</h3>

The arrays, which most of the time are used to represent the content of a memory, can be initialised by providing a .mem file. The C++ code which is generated does not depend of the content of the .mem file. The right .mem file is read to fill the array just before the simulation is run. Thus, the same executable file can be used with different memory contents.

The initialisation process depends on the context of the array (entity type name, entity instance name, parent type and parent instance name) and of the name of the RARRAY instance.

When the array belongs to an entity which itself belongs to a compound entity, the following criteria are tested in sequence and the first readable file with a name which matches one of the criteria is used:

- &lt;compound\_instance\_name&gt;.<entity\_instance\_name&gt;.&lt;array\_instance\_name&gt;.mem
- &lt;compound\_instance\_name&gt;.&lt;entity\_type\_name&gt;.&lt;array\_instance\_name&gt;.mem
- lt;compound\_type\_name&gt;.&lt;entity\_instance\_name&gt;.&lt;array\_instance\_name&gt;.mem
- lt;compound\_type\_name&gt;.&lt;entity\_type\_name&gt;.&lt;array\_instance\_name&gt;.mem

When the array's entity does not belong to a compound entity or when no file is found matching any of these criteria, the following two files are checked:

- &lt;entity\_instance\_name&gt;.&lt;array\_instance\_name&gt;.mem
- &lt;entity\_type\_name&gt;.&lt;array\_instance\_name&gt;.mem

**Examples:**



- Let us consider the instance **SEND** of type **Sender**. The entity Sender declares an RARRAY called**data**.  
Then, the file **SEND.data.mem** is used if it exists, otherwise the file **Sender.data.mem** is used. In the latter case, the same file may be used to initialise several instances (*e.g.* SEND2 of type Sender if the file SEND2.data.mem does not exist).

- Let us consider now the instance **NODE1** of type **Node,**.  The entity Node is defined as a COMPENTITY and has a child **SEND1** of type **Sender**.  
In that case each of the following file will be checked (in order) until finding one readable: **NODE1.SEND1.data.mem**, **NODE1.Sender.data.mem**, **Node.SEND1.data.mem**, **Node.Sender.data.mem**, **SEND1.data.mem**, **Sender.data.mem**.  

**Warning:** Sharing the same file for the initialisation does not mean that the arrays are physically shared. This mechanism is mainly useful when defining meshes with lots of nodes to avoid creating a different file for each node (The number of nodes may be important and may change).

**Notes:**

- The format of the data contained in the .mem file is checked during the initialisation process. It has to match the format declared in the EDL file. Otherwise, an error message occurs at the beginning of the simulation and the initialisation process is cancelled.

- Empty lines are ignored. 

- Lines starting with '//' are also ignored.

- if the *size* of the array is >0, no more than *size* elements are read.

### Initialisation and sections

A .mem file may contain sections.  The section separator is a line of the form: **//$next section**.

When a file contains sections (**no\_section**), the
**i**<sup>th</sup> array which has to be initialised from that file will be initialised with the content of the (**i modulo no\_section**)<sup>th</sup> section.<BR> The order of the arrays is determined by the declaration order of instances (AENTITY) in the STRUCTURE section part of the project definition file. The **//$next section** construct is most frequently used with MESH entities.  

[<- Defining a Project](<project.md>)

[<- Userguide](<Userguide.md>)

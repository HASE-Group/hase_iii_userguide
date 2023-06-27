## Defining a Project

A typical HASE project consists of a number of entities connected together by links attached to ports belonging to the entities, as shown in the project screen image below.

Most entities also have a set of parameters, *e.g.* a memory entity includes an array parameter which models the actual memory component within the entity.

The project definition file is structured as follows:

<pre>project ::= PROJECT (  
    <a href="https://github.com/HASE-Group/Documents/blob/main/preamble.md">preamble</a>  
    <a href="https://github.com/HASE-Group/Documents/blob/main/paramlib.md">paramlib</a>   
    <a href="https://github.com/HASE-Group/Documents/blob/main/globals.md">globals</a>  
    <a href="https://github.com/HASE-Group/Documents/blob/main/entitylib.md">entitylib</a>  
    <a href="https://github.com/HASE-Group/Documents/blob/main/structure.md">structure</a>  
)</pre>

![image of EMMA project](images/emma3.png)

[<- Userguide](<https://github.com/HASE-Group/Documents/blob/main/Userguide.md>)
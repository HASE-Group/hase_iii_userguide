## Defining a Project

A typical HASE project consists of a number of entities connected together by links attached to ports belonging to the entities, as shown in the project screen image below.

Most entities also have a set of parameters, *e.g.* a memory entity includes an array parameter which models the actual memory component within the entity.

The project definition file is structured as follows:  

project ::= PROJECT (  
&nbsp; &nbsp; &nbsp;  [preamble](<preamble.md>)  
&nbsp; &nbsp; &nbsp;  [paramlib](<paramlib.md>)  
&nbsp; &nbsp; &nbsp;  [globals](<globals.md>)  
&nbsp; &nbsp; &nbsp;  [entitylib](<entitylib.md>)  
&nbsp; &nbsp; &nbsp;  [structure](<structure.md>)  
  )

![image of EMMA project](images/emma3.png)

[<- Userguide](<Userguide.md>)

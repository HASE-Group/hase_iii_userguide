## ENTITY - Architectural Entity Description

### Synopsis

The **ENTITY** construct enables the creation of the various diferent types of entity required to construct an architecture.

### Syntax

ENTITY entity\_type\_name *library\_name* (  
      *[EXTENDS](<abstract.md>)* (*abstract\_type*)  
      DESCRIPTION ("description")  
      *INCLUDE* ("*compilation options*")  
		*STATES* (*state1, state2, ...*)  
      [PARAMS](<parameters.md>) ( *modifier reference_parameter*, ... )  
      [PORTS](<port.md>) ( port1, port2, ... )  
	 )  

- **entity\_type\_name** *str* - The name of the type of entity to be created.  This is used to create instances of the component in the architecture, and to identify the correct simulation code and icon.  
- **library\_name** *str (optional)* - The name of library from which the behaviour is to be taken. If the library\_name is not specified, then the user must provide a .hase description of the behaviour.  See [Clocks](<synclib.md>) for a description of components which are currently in the HASE library.  
- **abstract_type** *str (optional)* - The name of
the [abstract entity](<abstract.md> from which this entity is intended to inherit.  The abstract entity must have previously been defined in the [entity library](<entitylib.m>)  
- **description** *str* - A description of an aspect of the entity.  
- **compilation options** *str (optional)* - gives additional compilation information for this specific entity. For example, paths to find header files can be specified (e.g. -I${HASEDIR}/distributions/includejava).
- **states*** str (optional)* - This is a declaration of the entity's states.  It provides the entity with a variable called **my_state** which can take values declared in the STATES statement.  Each state can have a .gif images associated with it in the [.elf file](<elf.md>)
- **modifier** (optional) - There are three possible types of modifier: *final*; *read_only*; *static*.
Using the *final* modifier means that the value of
this parameter is never written to the trace file.  
The *static* modifier must be used when the parameter is to be shared by every instance of this entity.  
The *read_only* modifier is used when a parameter is declared which must not be modified by the user via the gui, the value can be updated only by the instance of the entity. In the example below, the *hit* parameter is declared *read_only*. The user cannot decide what the number of hits is.
- **reference_parameter** - This section of the entity definition describes the parameters that are associated with the entity. For example, a memory entity may have an array associated with it to hold the contents, and an integer to hold the access time of the component. Careful selection of the parameters can result in reusable components.  For example a cache entity might have parameters for its size, associativity and access time. See [reference parameters](<parameters.md>).
- **port** - This section of the entity definition describes the [ports](<port.md>) of the entity.  The ports are the entities' means of communicating with the other entities. Ports of different entities are connected together to form a [communication link](<paramlib.md>).

### Example
```
ENTITY Cache (  
	DESCRIPTION ( "A generic parameterized cache" )  
	PARAMS (  
		 RARRAY ( Memory, cache_contents )  
		 RINT ( access_time, 2 )
		 RENUM ( Assciativity, cache_assoc, 0 )
		 read_only RINT ( hit, 0 )
	 )
	PORTS (
		PORT ( to_cpu, LinkData, SOURCE )
		PORT ( from_cpu, LinkData, DESTINATION )
		PORT ( to_main_mem, LinkData, SOURCE )
		PORT ( from_main_mem, LinkData, DESTINATION )
	)
      );
```

[<- Entity Library](<entitylib.md>)

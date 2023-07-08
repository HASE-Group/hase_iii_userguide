## COMPENTITY - Compound Entity

### Synopsis

The **COMPENTITY** declaration enables the creation of higher level architecture components.  This is used to group together entities and/or other higher level entities (the COMPENTITY's descendants) along with their interconnections into one entity, allowing the operation of the lower level components to be abstracted out.

When a project is first opened, a compound entity is displayed on screen by its own icon. Left clicking on this icon changes the display to show the next level of detail (*i.e.* a descendant may itself be a compound entity).


### Syntax

```
compound ::= COMPENTITY entity_type_name_def ( 
    DESCENDANTS ( 
      { descendant ; }
    ) 
    [ EXTENDS ( abstract_type ) ]
    [ DESCRIPTION ( " description_txt " ) ]
    [ INCLUDE ( " compilation_options " ) ]
    [ STATES ( state { , state } ) ]
    [ PARAMS ( { entity_param } ) ]
    [ PORTS ( { port } ) ]
    [ ATTRIB ( { attrib_item } ) ]
  )
  
descendant ::= clink |
  CHILD ( 
    entity_type_name , entity_inst_name , attribs 
  )
```

- **entity\_type\_name\_def** *str* - The name of the type of higher level entity to be created. This is used to create instances of the component in the architecture and to identify the correct simulation code and icon.
- **descendant** - The descendant components describe which entities and their interconnections are to be used to compose the higher level.  Therefore the descendant_component can be of two types,
a *child entity* (see below) or a [link](<link.md>).
- **description\_txt** *str* - A description of an aspect of the higher level entity.
- **entity\_param** - *Optional* - This section of the higher level entity definition describes the parameters that are associated with the compound entity itself.
- **port** - *Optional* This section of the entity
definition describes the [ports](<port.md>) of the compound entity itself.

If there are any unconnected ports on the lower level entities these are inherited by the higher level entity. These two ports then behave as one, so if the higher level port is then linked to a different component, the lower level port is automatically connected too. This inheritance can be performed at multiple levels.

### CHILD - Compound Entity Component

### Synopsis

The **CHILD** construct provides a mechanism for defining lower level entities in a COMPENTITY definition. Ports that are left unconnected on lower level entities are inherited by the higher level entity. These two ports then behave as one, so if the top level port is then linked to another port then the lower level port is automatically linked too. This inheritance can be performed at multiple levels resulting in three or four or more ports all behaving as one.

- **entity\_type\_name** *str* - This specifies the type name of the entity to be used.  This entity must have already been defined in the entity library.
- **entity\_inst\_name** *str* - The instance name of the entity name to be added.  Note that instance names must be unique for a given type of entity. The same instance name can be given to entities of different types, but it should be avoided as it causes confusion.

### Example
```
COMPENTITY Computer (
    DESCENDANTS (
        CHILD ( Mem, main_mem )
        CHILD ( CPU, processor )
        CLINK ( CPU.processor[to_mem]->Mem.main_mem[from_proc], 3)
    )
    DESCRIPTION ( "A basic computer" )
    PARAMS ( 
        RARRAY ( Memory, cache_contents )
        RINT ( access_time, 2 )
        RENUM ( Associativity, cache_assoc, 0 )
    )
    PORTS ( 
        PORT ( to_cpu, LinkData, SOURCE )
        PORT ( from_cpu, LinkData, DESTINATION )
        PORT ( to_main_mem, LinkData, SOURCE )
        PORT ( from_main_mem, LinkData, DESTINATION )
    )
)
```

[<- Userguide](<Userguide.md>)


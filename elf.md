<a name="top"></a>
## Screen Layout

The screen layout in HASEIII is defined in an Entity Layout File containing information related to an entity (position, states, ports and parameters) and to a link (link corner positions) when displayed on screen.

An Entity Layout File contains a list of lines each of which should have one of the following formats:

- object_id : [POSITION](<#position>) (x,y,z) 
*info1 info2 info3*
- object_id : [STATES](<#states>) state1\_name gif\_image1 state2\_name gif\_image2 ...
- object_id : [PORT](<#port>) port\_name SIDE entity\_side POSITION port\_position
*position_string ICON icon_file*
- object\_id : [PARAM](<elf-param.html>) param\_name MODE mode\_name  POSITION (param\_x,param\_y)
- object\_id : [LINKCORNER](<corners.html>) src\_entity src\_port dest\_entity dest\_port no\_of\_corners *(cx1,cy1) ...*
- [bottomcorner](<bottom.html>) : position (x,y)

where **object_id** can be either an entity name (which has been declared in the entity library) or a component name defined as a CHILD of a COMPENTITY or as an AENTITY STRUCTURE.

<hr>

Note: The syntax given for the parts of the entity layout file may run onto a second line, however when creating a layout file, the part being specified, for example, a port's position, must not contain any new line characters in the specification.

<a name="position"></a>
## Entity Layout: POSITION

### Synopsis 

The POSITION declaration provides a mechanism for positioning entities in the project view panel.

### Syntax 

```
point_2d ::= ( x , y )

point_3d ::= ( x , y , z )

entity_position ::= object_id : POSITION point_2d [ infoX ] [ infoY ] [ layout_order ]
  | object_id : POSITION point_3d [ infoX ] [ infoY ] [ layout_order ]
```

 -  **object_id**  *str* - The name of the entity to be positioned. This can be the type name of an entity defined in the entity library, in which case the same coordinates will be given to all the entities of that type. It can also be the instance name of the required entity. The instance name should be the complete hierarchical name.  When specifying a level name from the hierarchy, the instance name, not the type name, should be used. Each level in the hierarchy should be separated by a '.',  *i.e.* the syntax for a complete hierarchical name is: 

**top\_level\_instance\_name.next\_level\_name. ... .last\_level\_name}**. 

The position given to an instance overrides the position information of the library component.

 -  **x**  *int* - The x coordinate of the top left corner of the entity's icon. This coordinate is relative to the top left corner of the project view panel for top level entities. For child entities of a compound entity it is relative to the upper left corner of the parent entity's icon. 

The x coordinate may also be a string. In that case the string has to be the name of one of the parameters specified for the related entity. Then the position of the icon will change when the value of the parameter is changed.

 -  **y**  *int* - The y coordinate of the top left corner of the entity's icon. This coordinate is relative to the top left corner of the project view panel for top level entities. For child entities of a compound entity it is relative to the upper left corner of the parent entity's icon. 

The y coordinate may also be a string. In that case the string has to be the name of one of the parameters specified for the related entity. Then the position of the icon will change when the value of the parameter is changed.

 -  **z**  *int* - The z depth of the entity's icon. This optional parameter maybe used to modify the render order of entities. If unspecified, the z coordinate will default to '0' (considered to be the front of the screen). A high value z will push the entity deeper into the screen. For child entities the z coordinate is relative to the parent entity.
 

 -  **infoX, infoY, layout_order**  *str*
Some entities ( *e.g.* meshes) have up additional items of layout information.

### Example 

<tt> Memory\_Hierarchy.secondary\_cache : POSITION (100,50)</tt>

[<- top](<#top>)

<a name="states"></a>
### Entity Layout: STATES

### Synopsis

The STATES declaration provides a mechanism for associating a gif image with a particular state of an entity.

### Syntax

object\_id : STATES state1\_name gif\_image1 state2\_name gif\_image2 ...

- **object_id** *str* - The name of the entity. This can be the type name of an entity defined in the entity library, in which case the state will be associated with all the entities of that type. It can also be the instance name of the required entity. The instance name should be the complete hierarchical name.  When specifying a level name from the hierarchy, the instance name, not the type name, should be used. Each level in the hierarchy should be separated by a '.', *i.e.* the syntax for a complete hierarchical name is:

**top\_level\_instance\_name.next\_level\_name. ... .last\_level\_name**.
- **state1\_name ***str* - One of the states defined in the ENTITY declaration.
- **gif_image1** * str* - The gif image to be associated with state1. HASE looks for this file in the project's *bitmaps* (image repository) subdirectory, then in HASE's own internal image repository.
If it cannot be found in either, the HASE default_ent.gif image is used. If no state is defined or no image is associated with a state, the name of the entity (not of the instance) is used to find the gif image.

### Example

Entity definition:

```
ENTITY memory  (
 DESCRIPTION ("Memory")
 STATES (M_IDLE, M_READ, M_WRITE)
 ...
        );
```

Entity layout:

```
MEMORY : position (350,30)
MEMORY : STATES M_IDLE:memory M_READ:mem_read  M_WRITE:mem_write
...
```
[<- top](<#top>)

<a name="port"></a>
## PORT - Communication Port Declaration

### Synopsis

The **PORT** construct enables the creation of communication ports for an entity. Ports from different entities are connected together via [links](<links.md>) allowing entities to exchange information.

The **XPORT** construct enables different instances of an entity to have different numbers of ports. This is often convenient for busses, for example.

### PORT Syntax

PORT ( port\_name, message\_type, *port\_type* )

- **port_name** *str* - This defines the name of the port to be created. It is used to identify the port when messages are to be sent from the entity and in the creation of links.  For any one entity the port_name must be unique, though the same port name can be used on different entities.
- **message_type** *str* - The message type defines the type of messages that the link connected to the port can handle. It should specify the type name of a [link parameter](<paramlib.md>) defined in the parameter library.
- **port_type** *str* - This specifies the type of the port, *i.e.* whether it is a source port or a destination port. This is specified by using **SOURCE** or **DESTINATION** in the PORT constructor.

### Example

PORT ( processor\_to\_memory, DataLink, SOURCE )

### XPORT Syntax

XPORT (no\_of\_ports, port\_name, message\_type*, port\_type* )

- **no\_of\_ports** - the default number of ports of this type; the actual number of ports (if different) is specified when an instance of the entity is created in the STRUCTURE section of the project definition file.

- **port\_name** - as for PORT, this defines the name of the port(s) to be created. Because XPORT defines more than one port, HASE automatically names these ports as port\_name0, port\_name1, *etc.*

-  **message\_type**, **port\_type** - as for PORT

### Example

```
ENTITY bus (
 PORTS (
   XPORT(2, input,l_bus,DESTINATION);
   XPORT(2, output,l_bus,SOURCE);
   )
        );
```

```
AENTITY bus BUS1 (
 DESCRIPTION("bus1")
  ATTRIB ( XPORT (input, 3) );
 );
```

This creates a bus (BUS1) with 3 input ports (input0, input1, input2) and 2 output ports (output0, output1).

[<- top](<#top>)

[<- Userguide](<Userguide.md>)

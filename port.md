## PORT - Communication Port Declaration

### Synopsis

The **PORT** construct enables the creation of communication ports for an entity.  Ports from different entities are connected together via [links](<link.md>) allowing entities to exchange information.

The **XPORT** construct enables different instances of an entity to have different numbers of ports. This is often convenient for busses, for example.

### PORT Syntax

PORT ( port\_name, message\_type, *port\_type* )

- **port_name** *str* - This defines the name of the port to be created. It is used to identify the port when messages are to be sent from the entity and in the creation of links.  For any one entity the port_name must be unique, though the same port name can be used on different entities.

- **message_type** *str* - The message type defines the type of messages that the link connected to the port can handle. It should specify the type name of a [link parameter](<paramlib.md#link>) defined in the parameter library.

- **port_type** *str* - This specifies the type of the port, *i.e.* whether it is a source port or a destination port. This is specified by using **SOURCE** or **DESTINATION** in the PORT constructor.

### Example

<tt>PORT ( processor\_to\_memory, DataLink, SOURCE )</tt>

### XPORT Syntax

XPORT (no\_of\_ports, port\_name, message\_type, *port_type* )

- **no\_of\_ports** - the default number of ports of this type; the actual number of ports (if different) is specified when an instance of the entity is created in the STRUCTURE section of the project definition file.

- **port\_name** - as for PORT, this defines the name of the port(s) to be created. Because XPORT defines more than one port, HASE automatically names these ports as port_name0, port_name1, *etc.*

-  **message\_type**, **port\_type** - as for PORT

### Example

```
ENTITY bus ( 
 PORTS (
   XPORT(2, input,l_bus,DESTINATION);
   XPORT(2, output,l_bus,SOURCE);
   )
	);

AENTITY bus BUS1 ( 
 DESCRIPTION("bus1")
  ATTRIB ( XPORT (input, 3) );
 );
```

This creates a bus (BUS1) with 3 input ports (input0, input1, input2) and 2 output ports (output0, output1).

[<- Defining a Project](<project.md>)

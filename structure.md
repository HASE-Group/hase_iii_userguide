## STRUCTURE - The definition of the Architecture

The **STRUCTURE** section of the project definition describes which entities from the entity library are to be included in the architecture description and how many of them there are. It also deals with how the entities are connected together.

- **architecture\_entity** - This defines a component (AENTITY) to be included in the architecture.  

- **communication\_channel** - This defines a link (CLINK) that interconnects AENTITIES in the architecture.

<HR>

## AENTITY - Architecture Entity

### Synopsis

The AENTITY construct provides a mechanism for adding components from the entity library to the architecture description.

### Syntax

<tt>AENTITY typename inst_name ( DESCRIPTION ( "description" ) )</tt>

- **typename** *str* - The type name of the entity in the [entity library](<entitylib.md>) to be included in the architecture description.

- **inst\_name** *str* - The instance name of the entity to be included. Entities of the same type must have different instance names.

- **description** *str* - A description of an aspect of the new architecture component.

**Example:**

```
STRUCTURE (
AENTITY cache Primary_Cache (
                DESCRIPTION ("A primary cache instance"))
		ATTRIB (RPARAM(clockName,CACHE_CLOCK)
                )
        )
```
- **clockName** allows the clock with which this instantiation of the entity is registered to be selected. This only needs to be specified if the clock to be used is a Pll, *i.e.* not the standard Clock with which an entity is automatically registered through the EXTENDS mechanism.  See [Clocks](<synclib.md>) for further details.

<hr>
<a name="clink"></a>

## CLINK - Communication links between entities

### Synopsis

The **CLINK** construct provides a mechanism to enable two different [ports](<port.md>) to be connected together. The ports maybe on the same or different entities.  The entities must have already been defined in the [entity library](entitylib.md>) section of the project definition and the instance of each entity involved must have been defined either as an AENTITY in the STRUCTURE section of the project definition or as a CHILD in a [compound entity](<compentity.md>).

### Syntax

<tt>CLINK ( src\_type.src\_inst[src\_port]->dest\_type.dest\_inst[dest\_port]*, link_width* )</tt>

- **src\_type** *str* - The type name of the source entity to be connected.

- **src\_inst** *str* - The instance name of the source entity to be connected. The type and instance name combination must be unique.

- **src\_port** *str* - The name of the port on the source to be connected.

- **dest_type** *str* - The type name of the destination entity to be connected.

- **dest\_inst** *str* - The instance name of the destination entity to be connected. The type and instance name combination must be unique.

- **dest\_port** *str* - The name of port on the destination to be connected.

- **link\_width** *str* - The width of the link in pixels. This is used when displaying the link. If the width is 0, the link is not drawn and packets travelling along the link are not displayed.

### Example

<tt> CLINK ( CPU.cpu4[to\_memory]->Memory.main\_memory4[from\_cpu], 3 )</tt>

[<- Defining a Project](<project.md>)

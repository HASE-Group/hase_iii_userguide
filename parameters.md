<a name="list"></a>
## Reference Parameters

Reference parameters are used to declare variables of a HASE supplied type (bool, char, integer, unsigned integer, long, floating-point, string and range):

- [RBOOL](<parameters.md#rbool>) ( instance\_name, initial\_value )
- [RCHAR](<parameters.md#rchar>) ( instance\_name, 'initial\_char' )
- [RINT](<parameters.md#rint>) ( instance\_name, initial\_value )
- [RUINT](<parameters.md#ruint>) ( instance\_name, initial\_value )
- [RLONG](<parameters.md#rlong>) ( instance\_name, initial\_value )
- [RFLOAT](<parameters.md#rfloat>) ( instance\_name, initial\_value )
- [RSTRING](<parameters.md#rstring>) ( instance\_name, "initial\_string" )
- [RRANGE](<parameters.md#rrange>) ( instance\_name, min, max, initial\_value )

or of a parameter type previously defined in the [parameter library](<paramlib.md>) section of the
project definition file:

- [RENUM](<parameters.md#renum>) ( typename, instance\_name, initial\_value )
- [RSTRUCT](<parameters.md#rstruct>) ( typename, instance\_name )
- [RINSTR](<parameters.md#rinstr>) ( typename, instance\_name )
- [RLINK](<parameters.md#rlink>) ( typename, instance\_name )
- [RARRAY](<parameters.md#rarray>) ( typename, instance_name, no\_of\_elements )

When  a reference parameter is declared in the PARAMS section of an [entity](<entity.md>), it may be preceded by a modifier
(*final*, *read\_only* or *static*).

<hr>

- **typename:** *str* - The name of the type to be used (ENUM for RENUM, BIT for RBIT, ...).
- **instance_name:** *str* - The instance name of the parameter to be created. 
- **initial_value:** *int* - The initial value of the parameter.

<hr>

<a name="rbool"></a>
<font size="+2">**rBool** - Reference Boolean Parameter</font> - RBOOL ( instance\_name, *initial\_value*)  
 
The RBOOL declaration allows the user to create boolean parameters. Note that there is no corresponding BOOL definition in the parameter library because 'bool' is a standard type and is known to HASE. For this reason no type name is supplied to the RBOOL constructor.

Allows bitwise manipulation of an integer via the GUI.

**Example:** RBOOL ( my_flag, FALSE )  
[<- top](<parameters.md#list>)
<hr>

<a name="rchar"></a>
<font size="+2">**rChar** - Reference Character Parameter</font> - RCHAR ( instance_name, *'initial_char'*) 
 
The RCHAR declaration enables the user to create character parameters. Note that there is no corresponding CHAR definition in the parameter library because 'char' is a standard type and is known to HASE. For this reason no type name is supplied to the RCHAR constructor.  
**Example:** RCHAR ( mode, 'a' )  
[<- top](<parameters.md#list>)
<hr>

<a name="rint"></a>
<font size="+2">**rInt** - Reference Integer Parameter</font> - RINT ( instance\_name, *initial\_value*)  

The RINT declaration enables the user to create integer parameters. Note that there is no corresponding INT definition in the parameter library because 'integer' is a standard type and is known to HASE. For this reason no type name is supplied to the RINT constructor.

Integers normally appear in decimal format in the display. If the *initial_value* is prefaced by 0x, the value is displayed in hexadecimal with leading zeros suppressed; if the *initial_value* is prefaced by 0X, all 8 hexadecimal characters are displayed.  

 **Examples:** RINT ( clockPeriod, 10 ), &nbsp;
RINT ( CODEWORD, 0X0 )  
[<- top](<parameters.md#list>)
<hr>

<a name="ruint"></a>
<font size="+2">**rUint** - Reference Unsigned Integer Parameter</font> - RUINT ( instance_name, *initial_value*)

The RUINT declaration enables the user to create unsigned integer parameters.  Note that there is no corresponding UINT definition in the parameter library because 'unsigned' is a standard type and is known to HASE. For this reason no type name is supplied to the RUINT constructor.

Unsigned integers can be displayed in decimal or hexadecimal format, as for integers (see RINT, above).

**Example:** RUINT ( memory_size, 12 )  
[<- top](<parameters.md#list>)
<hr>

<a name="rlong"></a>
<font size="+2">**rLong** - Reference Long Integer Parameter</font> - RLONG ( instance_name, *initial_value*)

The RLONG declaration enables the user to create long integer parameters.  Note that there is no corresponding LONG definition in the parameter library  because 'unsigned' is a standard type and is known to HASE. For this reason no type name is supplied to the RLONG constructor.

Long integers can be displayed in decimal or hexadecimal format, as for integers (see RINT, above).

**Example:** RLONG ( memory_size, 12 )  
[<- top](<parameters.md#list>)
<hr>

<a name="rfloat"></a>
<font size="+2">**rFloat** - Reference Floating-Point Parameter</font> 
- RFLOAT ( instance\_name*, initial\_value*)<p>

The RFLOAT construct provides a mechanism for creating floating-point variables in an architecture definition. There is no floating-point equivalent construct in the parameter library as, like integers, floating-point is a type known by HASE. For this reason no type name is supplied to the RFLOAT constructor.

**Example:** RFLOAT ( Average_Access_Time, 3.4 )  
[<- top](<parameters.md#list>)
<hr>

<a name="rstring"></a>
<font size="+2">**rString** - Reference String Parameter</font>  - RSTRING ( instance_name, "initial_string", )

The RSTRING declaration provides a method of creating string parameters. There is no  STRING equivalent construct in the parameter library as, like integers, string is a type known by HASE. For this reason no type name is supplied to the RSTRING constructor.

**Example:**
RSTRING ( Cache\_Associativity, "Fully\_Associative" )  
[<- top](<parameters.md#list>)
<hr>

<a name="rrange"></a>
<font size="+2">**rRange** - Reference Range Parameter</font>  - RRANGE ( instance_name, min, max, initial_value)

The RRANGE construct provides a mechanism to create an integer parameter, the value of which is constrained within boundaries. The initial default value is set to *initial\_value*.  The graphical
interface is expected to show a slider allowing the user to alter the parameter value. It should not allow this parameter to be set to a value outwith the range defined by *min* and *max* boundaries. Although animation and experiment tools may prompt a warning message in case of out of bounds definition, there is no automatic checking during the simulation.

**Example:** RRANGE ( access_time, 1, 10, 5 )  
[<- top](<parameters.md#list>)
<hr>

<a name="renum"></a>
<font size="+2">**rEnum** - Reference Enumerated Type Parameter</font> - RENUM ( typename, instance\_name, initial\_state)

The RENUM declaration provides a mechanism for creating variables of an enumerated type [ENUM](
paramlib.md#enum>)previously defined in the parameter library.

If the enumerated parameter is to be displayed as a port icon, then the instance name should start with **port** and end with the name of the port to be changed. For example if an entity has a port named *OUT* then the parameters instance name should be *portOUT*.

**Example:** RENUM ( cache\_state, secondary\_cache, 0 )  
[<- top](<parameters.md#list>)
<hr>

<a name="rstruct"></a>
<font size="+2">**rStruct** - Reference Structure Parameter</font> - RSTRUCT ( typename, instance_name)

The RSTRUCT declaration allows the creation of instances (variables) of a structure [STRUCT](<paramlib.md#struct>)previously defined in the parameter library.

**Example:** RSTRUCT ( Data\_Packet, message )  
[<- top](<parameters.md#list>)
<hr>

<a name="rinstr"></a>
<font size="+2">**rInstr** - Reference Instruction Parameter</font> - RINSTR ( typename, instance_name)

The RINSTR declaration gives a method of creating instances of an instruction set [INSTR](paramlib.md#instr>) previously defined in the parameter library.

**Example:** RINSTR ( instr\_set, SPARC\_Instruction\_Set )  
[<- top](<parameters.md#list>)
<hr>

<a name="rlink"></a>
<font size="+2">**rLink** - Reference Link Parameter</font> - RLINK ( typename, instance\_name )

The RLINK construct enables the creation of a link parameter of a type [LINK](<paramlib.md#link>) defined earlier in the parameter library.

**Example:** LINK ( DLink, link_data )  
[<- top](<parameters.md#list>)
<hr>

<a name="rarray"></a>
<font size="+2">**rArray** - Reference Array Parameter</font> - RARRAY ( typename, instance_name, no_of_elements )

The RARRAY construct provides a mechanism for declaring parameters of an array type [ARRAY](<paramlib.md#array>) or [ARRAYI](<paramlib.md#arrayi>) previously defined in the parameter library.

**Example:** RARRAY ( Memory, main_memory, 65536 )  
[<- top](<parameters.md#list>)

Arrays can be automatically initialised at the start of a simulation by creating a corresponding <tt>.mem</tt> file, *e.g.* to initialise the data memory array incorporated into a data memory <tt>D_MEMORY</tt>, declared as follows,

```
ENTITYLIB (
  ENTITY d_memory (
     PARAMS (
             RINT (D_Mem_Size, 65536);
             RARRAY ( data_mem_array, data_mem, D_Mem_Size);
    )
   );
  )

STRUCTURE (
      AENTITY d_memory D_MEMORY (
   	  DESCRIPTION("Data memory")
      );
  )
```
requires a file called: <tt>D\_MEMORY.data\_mem.mem</tt>

More details can be found in [Initialising Arrays](<arrays.md>)  
[<- top](<parameters.md#list>)

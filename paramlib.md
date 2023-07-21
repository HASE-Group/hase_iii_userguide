## PARAMLIB - The Parameter Library

The parameter library is used to create the type definitions of parameters used by the project. HASE supplies the built-in types: bit, bool, char, integer, unsigned integer, long, floating-point, string and range, from which more complex types can be created, for example structures, enumerations and ranges. Once types have been defined in the parameter library, instances of these types can then be created in the GLOBALS section of the project definition file and within entity declarations (in the ENTITYLIB section).


[ENUM](<#enum>) ( typename, [ enumerate *: icon*, ... ] )  
[STRUCT](#struct) ( typename, [ enumerate *: icon*, ... ] )  
[INSTR](#instr) ( typename, [ (tag, reference_parameter) , ... ] , class_enum )  
[LINK](#link) ( typename, [ ( tag, reference_parameter *: icon*), ... ] )  
[ARRAY](#array)( typename, type_of_element )  
[ARRAYI](#arrayi)( typename, type_of_element )  

<hr>

<a name="enum"></a>
<font size="+2">**Enum** - Enumerated Type Parameter</font>  - ENUM ( typename, [ enumerate *: icon*, ... ] )  

The ENUM declaration enables the creation of an enumerated type similar to those found in C and C++. It provides a means of enumerating a finite set of elements.

- **typename:** *str* - The name of the enumerated type, used when creating instances.  
- **enumerate:** *str* - A unique label for each part of the set.  
- **icon:** *str* - The file name of the icon of the associated enumerate.  Note that this does not need to include a full path name as it is assumed that the icon is in the *bitmaps* sub-directory of the current projects working directory (as specified
in the preamble). Also the .btm and .gif extensions can be missed off the icon name as these are automatically added (gif files are searched for first).

**Example:**
```
ENUM ( cache_state, [ cache_hit : hit_icon, cache_miss : miss_icon ] )
```  

<HR>

<a name="struct"></a>
<font size="+2">**Struct** - Structure Parameter</font> - STRUCT ( typename, [ reference_parameter , ... ] )  

The STRUCT construct allows the creation of data structures, similar to the struct declaration in C and C++.  This mechanism provides a means to aggregate variables of different types.  

- **typename:** *str* - The name of the structure, used when creating instances.  
- **reference_parameter:** - See the Reference Parameters section.  
**Example:**
```
STRUCT ( Data_Packet, [ RINT (Packet_No, 0) , RINT (Packet_Data, 0) ] )
```

<HR>

<a name="instr"></a>
<font size="+2"> **Instr** - Instruction Parameter</font>  

The **Instr** parameter is best explained by means of an example. The following example shows the full instruction set of a simple computer (described in more detail in <a href="s-comp.html">Example Project</a>), preceded by the necessary operand <tt>STRUCT</tt> definitions. Each instruction consists of a function and zero, one or more operands.
```
-- operands for loading a register from memory using address + (index reg)  
   STRUCT (t_load, [RSTRING (dest_reg, "-", 5),
                    RSTRING (src_reg, "-", 5),
                    RINT (address, 0)]);  
-- operands for storing a register to memory using address + (index reg)  
   STRUCT (t_store, [RSTRING (src_reg, "-", 5),
                     RSTRING (dest_reg, "-", 5),
                     RINT (address, 0)]);  
-- operands for loading a register from memory using address + (index reg)  
   STRUCT (t_load, [RSTRING (dest_reg, "-", 5),
                    RSTRING (src_reg, "-", 5),
                    RINT (address, 0)]);  
-- operands for storing a register to memory using address + (index reg)  
   STRUCT (t_store, [RSTRING (src_reg, "-", 5),
                     RSTRING (dest_reg, "-", 5),
                     RINT (address, 0)]);  
-- operand for loading register with immediate  
   STRUCT (t_loadi, [RSTRING (dest_reg, "-", 5),
                     RINT (immediate, 0)]);  
-- operands for move instruction  
   STRUCT (t_move, [RSTRING (dest_reg, "-", 5),
                    RSTRING (src_reg, "-", 5)]);  
-- operands for ALU scalar instruction group  
   STRUCT (t_alu,  [RSTRING (dest_reg, "-", 5),  
                     RSTRING (src_reg1, "-", 5),  
                     RSTRING (src_reg2, "-", 5)]);  
-- operands for ALUI scalar instruction group  
   STRUCT (t_alui,  [RSTRING (dest_reg, "-", 5),
                     RSTRING (src_reg1, "-", 5),
                     RINT (immediate, 0)]);  
-- operands for COMP instruction  
  STRUCT (t_comp, [RSTRING (src_reg1, "-", 5),
                   RSTRING (src_reg2, "-", 5)]);  
-- Definition of a simple instruction set  
   INSTR (t_simple_instrn_set, [(NOP),  
         (JUMP, RINT (immediate, 0)),  
         (JREG, RSTRING (src_reg,  "-")),  
         (SETCC(SEQ,SNE,SGT,SLT,SGE,SLE), RSTRUCT (t_comp, comp_field)),  
         (BRANCH, RSTRING (label, "-", 50)),  
         (LDM, RSTRUCT (t_load,  load_field)),  
         (LDI, RSTRUCT (t_loadi, loadi_field)),  
         (STM, RSTRUCT (t_store, store_field)),  
         (ALU(ADD,SUB,MUL,DIV,AND,OR,XOR,SLL,SRL,SRA), RSTRUCT (t_alu, alu_field)),  
         (ALUI(ADDI,SUBI,MULI,DIVI,ANDI,ORI,XORI,SLLI,SRLI,SRAI), RSTRUCT (t_alui,  alui_field)),  
         (STOP) ], function);  
```

**Notes**

1. <tt>t_simple_instrn_set</tt> is the (*str*) **typename** of the instruction set, used when creating instances; a project can include more than one instruction set definition.

2. In <tt>STRUCT (t_loadi, [RSTRING (dest_reg, "-",
 5), RINT (immediate, 0)]);</tt>,  <tt>"-"</tt> is the initial value of <tt>dest_reg</tt> while the value <tt>5</tt></font> limits the length of the string to 5 characters; the default string length in HASE is 100 characters, so this parameter reduces the storage requirements of a project (*NB:* the length must allow for the end-of-string character).

3. In <tt>(JUMP, RINT (immediate, 0))</tt>, the operand is just an integer, so doesn't need a <tt>STRUCT</tt> definition.

4. <tt>NOP</tt> and <tt>STOP</tt> have no operand.

5. In <tt>
(ALU(ADD,SUB,MUL,DIV,AND,OR,XOR,SLL,SRL,SRA)</tt>, <tt>ALU</tt> is the (*str*) **tag** of a class of instructions, while <tt>ADD</tt>, *etc.* are members of the **class_enum** (in this case called function);  
this allows the construct <tt>if (IR.decode_ALU())</tt> to be used in *e.g.* the processor Hase++ file (*proc.hase*) when a common set of actions is to be performed for all <tt>ALU</tt> functions, where IR is an instruction
register of type <tt>t_simple_instrn_set</tt>.

6. Individual instructions within the <tt>ALU</tt> set can be decoded using a <tt>switch</tt> construct:
```
 switch (IR.function)  
  {
   case ADD:	// ADD Rx Ry Rz: Rx = Ry + Rz 
   dest_op = src1_op + src2_op; 
   break; 
   *etc.* 
  }
```
*OR* by using the construct:
```
if (IR.function == t_simple_instrn_set::JUMP) 
 {  
 }  
```
7. The semantics of the instructions are determined by the behavioural code in the Hase++ files. Thus the above definitions could be used as a basis for creating the instruction set of the simple processor described in <a href="project.html">Defining a Project</a>.

<HR>

<a name="link"></a>

<font size="+2">**Link**</font> - LINK ( typename, [ ( tag , reference_parameter *: icon*), ... ] )  

- **typename:** *str* - The name of the link parameter, used when creating instances and assigning types to individual ports.

- **tag:** *str* - A tag, which has an associated parameter, allows different types of messages to be passed down the same link. A 'send' function is created for each link parameter tag, which can then be used to send information of the associated parameter type to another entity.

- **reference_parameter:** - The definition of a message type that is associated with a tag.  See the Reference Parameters section.

- **icon:** *str* - The file name of the icon of the associated enumerate. Note that this does not need to include a full path name as it is assumed that the icon is in the *bitmaps* sub-directory of the current projects working directory (as specified in the preamble). Also the .btm and .gif extensions can be missed off the icon name as these are automatically added (gif files are searched for first).

**Example:**  
 
```
LINK ( DLink, [ ( MEM_R, RINT ( Address, 0) ),  ( MEM_W, RSTRUCT ( Info, I ) : PktIcon ) ] )
```
<HR>
<a name="array"></a>

<font size="+2">**Array** - Array Parameter</font> - ARRAY ( typename, type_of_elements )  

The ARRAY construct provides a mechanism for defining lists of objects of the same type, similar to the array construct in C & C++. Anytime such a declaration is encountered, a new C++ class will be created. This class will have the name **typename**.

- **typename:** *str* - The name of the array type, used when creating instances. Instances can either be created in C++ (using any existing class) or using <a
href="parameters.html#rarray">RARRAY</a>. Using RARRAY allows the display of arrays in the HASE graphical interface. It is strongly recommended that this be done.

- **type_of_elements:** *str* - The name of the type of the elements in the list.  This should be a HASE built-in type or a type previously defined in the parameter library section.

**Example:**  
 
```
ARRAY (data_mem_array, int);
```
<hr>
<a name="arrayi"></A>

<font size="+2">**ArrayI** - Instruction Array Parameter</font> - ARRAYI ( typename, type_of_elements )<BR><BR>

The ARRAYI construct provides a mechanism similar to ARRAY for defining lists of objects of the same type but specific to the ARRAYI construct is the ability to produce labels to address individual instructions within arrays of instructions (typically in memory).

- **typename:** *str* - The name of the array type, used when creating instances. Instances can either be created in C++ (using any existing class) or using <a
href="parameters.html#rarray">RARRAY</a>. Using RARRAY allows the display of arrays from the HASE graphical interface. It is strongly recommended that this be done.

- **type_of_elements:** *str* - The name of the type of the elements in the list.  This should be a HASE built-in type or a type previously defined in the parameter library section.

**Example:**
``` 
ARRAYI (instr_mem_array, t_dlx_instrn_set);
```
**Labels**

Labels are automatically built from the symbolic name associated with the ARRAYI construct. These labels can be then used in Hase++ code by means of a method called *process*. The signature of this method is as follows:

```
process(char * symbolicName, int offset)
```
The offset refers to a cell in the array of instructions bound to the symbolic name of the first parameter.

**Hase++ Example:**  
```
int addr = instr_mem.process(InstrMemRequest.addr.label, InstrMemRequest.addr.offset);
```

**Instruction Memory Example:**  
```
label2:
        ADDI R7 R7 42
        BNEZ R7 label2
        SUBI R7 R7 126
        J label3
        ADDI R8 R0 68
label3:
        ADDI R8 R0 17
```

[<- Defining a Project](<project.md>)

[<- Userguide](<Userguide>)

###  ABSTRACT - Abstract Entity Definition

### Synopsis

The **ABSTRACT** entity type creates an abstract entity with an interface described as a set of services (all methods which must be defined by the inheriting [entity](<entity.md>)).  The basic behaviour of such an entity is still described under the <tt>\$body</tt> label in the corresponding .hase file.

### Syntax


ABSTRACT name *library_name* (  
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; DESCRIPTION ( "description" )  
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; INTERFACE ( SERVICE ( service_name1, *param1*, ...), ... )  
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; *PARAMS ( reference\_parameters*, ...)  
&nbsp; &nbsp;	)  

- **name** *str* - The name of the abstract entity.
- **library_name** *str (optional)* - The name of the library from which the behaviour is taken. If the library_name is not specified, then the user must provide a .hase description of the behaviour.  The HASE library currently contains the following:
 - [sync](<synclib.md>) - contains basic entities to deal with clocking and synchronisation issues.
- **description** *str* - The description of the abstract entity.
-  **service_nameX** *str* - The name of a service to be defined by the inheriting entity.
- **paramX** *str (optional)* - Services may have some parameters. In that case, the service name is followed by a comma and a list of parameters separated by a comma. Each parameter must have a type followed by a name. Example: SERVICE (tick, int no) defines the service tick with one integer parameter named no. The .hase description will be able to use the parameter 'no'.  The abstract entity description must call the service tick with an integer parameter. Example: tick(12);
- **reference_parameter** - This section of the abstract definition describes the parameters that are associated with the abstract entity. These parameters will be inherited by the inheriting concrete class (see [entity](<entity.md>)).  For example, a memory entity may have an array associated with it to hold the contents, and integer to hold the access time of the component. Careful selection of the parameters could result in reusable components.  For example a cache entity might have parameters for its size, associativity and access time.

### Example

The abstract entity *clocked* (the Hase++ code for which is shown at the bottom of this page) is defined as follows:

```
ENTITYLIB (
ABSTRACT clocked (
DESCRIPTION ("two phases clock entities")
INTERFACE(
SERVICE (pre)
SERVICE (phase0)
SERVICE (phase1)
)
)

```

Ordinary (concrete) entities can inherit the services offered by *clocked* (*i.e.* **pre**, **phase0** and **phase1**) by including in their definition the statement

<tt> EXTENDS (clocked)</tt>

```
$class_decls  

//entity references  
sim_entity_id bus;  
sim_entity_id add_res;  
sim_entity_id mul_res;

//structures and variables  
t_rs_control Instruction, Register;  
int RS_no;  
int output_to_au;

//methods  
void Output_to_au();

\$class_defs

void rsc::Output_to_au()
{
  // Invoked by a Reservation Station: set output_to_au = 1  
output_to_au = 1;
}

$pre

bus = sim.get_entity_id("CDB");
add_res = sim.get_entity_id("ADD_RES");
output_to_au = 0;

cur_state = RSC;
strcpy(FUNC, "FREE");
dump_state();

$phase0
// predicate
sim_from_port receive(input);

sim_hold(5);
	// If there is an event from the CDB, copy function and tag number
	// into Register.

if (sim_waiting(ev, receive) > 0)
{
SIM_CAST(t_rs_control, Instruction, ev);  
Register.instrn = Instruction.instrn;  
Register.tag_no  = Instruction.tag_no;  
if (Register.instrn.function == VOID)  
strcpy(FUNC, "FREE");  
else if (Register.instrn.function == ADD)  
strcpy(FUNC, "ADD");  
else if (Register.instrn.function == SUB)  
strcpy(FUNC, "SUB");  
else if (Register.instrn.function == MUL)  
strcpy(FUNC, "MUL");  
else if (Register.instrn.function == DIV)  
strcpy(FUNC, "DIV");  
dump _state();  
}

$phase1

// if output_to_au is set, send content of register to arithmetic unit  
if (output_to_au == 1)  
{  
send_CONTROL(output, Register);  
output_to_au = 0;  
}
```

The code for *clocked* (in **clocked.hase**) is as follows:

```
$class_decls
int ClockPhase;
int DONE;
//clock reference
sim_entity_id clk;

$startup
clk = sim.get_entity_id("CLOCK");
((clock *)sim.get_entity(clk))->registering(get_id());

$body
pre();
DONE = 1;

// predicate
sim_from_p start(clk);

while (1) {
sim_wait_for(start, ev);

SIM_CAST(int, ClockPhase, ev);	//to avoid memory leakage

if (ClockPhase == 0)	// First phase of clock
phase0();
else // Second phase of clock
phase1();

sim_schedule( clk, 0.0, CLOCK, SIM_PUT(int, DONE));
}
```

[<- ENTITYLIB](<entitylib.md>)

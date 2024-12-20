## Hase++

Hase++ appears to the programmer as a superset of C++ containing simulation methods that allow entities to change state, to update parameter values, to send and receive packets, and to execute other emulation tasks. Each HASE Entity has a single Hase++ file associated with it. The HASE++ file is divided into several ‘$named_section’s. Each named section is known as a Service. The choices to be made regarding the use of some of these services depend on whether or not the entity is extended to use one of HASE's built-in clocking mechanisms. By default, all entities provide the following Services:

### Standard Services

#### $extra_classes_defs
Used to forward define any types or global variables needed that were not declared in the EDL. Anything declared in this section will be visible to the $startup, $report, and $body sections in the generated c++ file for the given entity.

#### $startup
This section is called in all entities before simulation starts. All entities will complete execution of this section before the first-time step of the simulation is launched.

#### $report
After simulation has finished, this section is called. It can be utilised to capture statistics or dump entity states to assist in debugging, or evaluation of the simulation activities.

#### $class_decls
Used to add declarations to the entity's class declaration. This can be used to add new member variables, functions, or local type definitions to the entity's generatred class. Any added new member function will need to have its definition declared in the class_defs Service.

~~~~~~~~~~~~~~~~~
$class_decls

unsigned int m_uCounter;
void Free();
~~~~~~~~~~~~~~~~~

Note that new member variables should be initilsed in the start of the $startup Service (or $pre Service, if the entity is extending one of HASE's Clocked or BiClocked entities).

#### $class_defs
Used to define any functions or static member variables that were declared in the $class_decls section.

~~~~~~~~~~~~~~~~~
$class_defs

void EntityName::Free()
{
	m_uCounter--;
}
~~~~~~~~~~~~~~~~~

#### $class_includes
Used to declare other classes or types that will be used by the entity's class declaration.

#### $body
Appropriate for entities that are not clocked. Code is this section is repeatedly called during the execution of the simulation.

Entities may also extend various abstract entity to provide additional services. 

### Clocked/Pclocked extension

#### $pre
This section is called after $startup, but before the first $tick.

#### $tick
In entities that use the Clocked or Pclocked extenion, $tick should be used instead of $body. Instead of being repeatedly called in a busy loop, the code in this section is called once for each tick (time step) of the clock.

### Biclocked/BiclockedD/Pbiclocked extension

#### $pre
Section called after $startup, but before the first $phase0/$phase1.

#### $phase0
In entities that use the Biclocked, BiclockedD or Pbiclocked extensions, $phase0 and $phase1 should be used instead of $body. Instead of being repeatedly called in a busy loop, each phase is called once for each time step of the clock. The two phases represent clocking logic on the rising and falling edges of a clock.

#### $phase1
See $phase0.

### $body, $tick, and $phase0/$phase1
Most of the code written in Hase++ will be in the $body, or in clocked entities, in the $tick or $phase0/$phase1 Service sections. Activity in these sections should emulate the functional behaviour of the entity being simulated. Typical activities will include Changing States, Updating Parameters, Sending Messages to other entities, and Receiving Messages from other entities. Results of these activities will cause trace file data to be written. This can subsequently be used to generate a visualisation of the execution.

### Visualisation

Once a simulation has been run, the **tracefile** generated by that simulation can be loaded back into HASE and used to animate the project display, allowing the user to see what happened during the simulation. Three types of event can be visualised: state changes, changes to parameter values and the movement of packets between entities.

### State Changes

For each entity, HASE automatically creates a <b>my\_state</b> variable to which any of the states declared for that entity in the project definition file can be assigned. A change of state will only be recorded in the tracefile, however, when a subsequent
<b>dump\_state()</b> statement is executed.  In the [Example Project](<comp.md>) included in this Userguide, the memory entity has the states **M\_IDLE** and **M\_BUSY**. Since **M\_IDLE** is declared first, <b>my\_state</b> will initially be set to **M\_IDLE** and the icon assigned to this state in the screen layout (*.elf*) file) will be the one drawn when the project is first loaded.  To change this state to **M\_BUSY** once the Memory has received a packet, the following statements are used in *memory.hase*:

```
 my_state = M_BUSY;  
 dump_state();
```

### Parameter Updates

An entry created by a <b>dump\_state()</b> statement also records in the tracefile entry the current values of the parameters (other than global parameters and array parameters) declared for that entity in the entity definition file.

### Updating Global Parameter Values

An entry is created in the tracefile for the current value of any global parameters declared in the model when a <b>dump\_globals()</b> statement is executed. Since global parameters can be accessed by any entity in the model, a mutual exclusion mechanism is provided to ensure proper synchronisation (see [GLOBALS](<globals.md>)).

### Updating Array Parameter Values

Updating array parameter values is less straightforward than updating *e.g.* integer parameters. For display purposes, HASE creates a separate copy of each array. To update both the display of the array parameter and the array itself, the **Update** function is used.  For example, in:

```
data_mem.Update(mem_address, write_data);
```

<b>write\_data</b> is a new value being written into location <b>mem\_address</b> of the array <b>data\_mem</b>. <b>write\_data</b> must have the same type as the elements of the array.

The Update function can have a third parameter, <b>step</b>, that allows events that occur at the same time to be ordered in the animation. For example, if two messages are sent at the same time, the one with the lowest step number will be animated first.  If they have the same step number they will be animated at the same time. The default value is 0.

### Sending Packets

Packets may be sent using using two different methods, one of which causes packets to be sent along specific links between entities and one which causes packets to be sent between entities that do not have specific links connecting them.

#### send\_*link\_pkt\_type*

This method schedules an event to a port (and thus to be sent along the link attached to that port) and causes an entry to be made in the trace file so that the packet can be visualised, *i.e.* seen moving along the link during animation. (This visualisation can be inhibited by declaring the link to be of width 0.)

##### Syntax
```
   send_*link_pkt_type*(port, pkt_name)
```

- <b>link\_pkt\_type</b>: the type of packet to be sent; this type is defined inside the LINK definition for the type of the link attached to the port, *e.g.*  
<tt>LINK(l\_bus,[(RESULT, RINT(value,0))]);</tt>  
which defines a link of type <b>l\_bus,</b> along which packets of type <b>RESULT</b> (containing an integer with type\_name *value*) can be sent.
- <b>port:</b> name of the port used to send the event.
- <b>pkt\_name:</b> the name of the packet to be sent. This packet must be of the same type as <b>link\_pkt\_type</b>.

#### sim\_schedule

This method schedules an event to an entity; it does not cause an entry to be made in the trace file. <b>sim\_schedule</b> is normally used in conjunction with SIM\_PUT (which is strictly speaking a Hase++ macro since it invokes more primitive Hase++  functions)

##### Syntax

 <tt>sim\_schedule(entity\_name, delay, link\_pkt\_type, SIM\_PUT(pkt\_type, pkt\_name));</tt>

##### Example

<tt>sim\_schedule(pipe, 0.0, INSTRUCTION, SIM\_PUT(t\_instrn\_reg, ID\_Input\_Reg));</tt>

- <b>entity\_name:</b> the local name for the entity to which the event is to be sent. The following is an example of how this name can be set up:

```
 $class_decls
 	sim_entity_id pipe;
 $pre
 	pipe = sim.get_entity_id("PIPE_DISP");
```

- <b>delay:</b> delay after which the event is sent.  
- This delay must be expressed in simulation time. 0 means in the next micro-cycle.
- <b>link\_pkt\_type:</b> the type of packet to be sent; this type is defined inside the LINK definition for the type of the link attached to the port, *e.g.*
     <tt>LINK(l\_pipe,[(INSTRUCTION, RSTRUCT(t\_instrn\_reg,DP))]);</tt>  
which defines a link of type <b>l\_pipe,</b> along which link packets of type <b>INSTRUCTION</b> (containing a struct with type\_name <b>t\_instrn\_reg</b>) can be sent.
- <b>pkt\_type:</b> the type of the packet to be sent; this packet must be of the same type as <b>link\_pkt\_type</b>.
- <b>pkt\_name:</b> the name of the packet to be sent.

### Receiving Packets

HASE places packets sent by entities in an event queue. Receiving entities use a number of methods to dequeue these events and to extract the relevant information from the packets.

#### Predicates

Predicates can be used to filter incoming events. The criterion can be the incoming port (sim\_from\_port), the tag carried by the event(sim\_type\_p) or a user-defined predicate. Predicates are typically used within a <b>sim\_waiting</b> command (see below).

#### sim\_waiting

<b>sim\_waiting</b> computes the number of events (from within the queue of events that are waiting to be processed) that are for the entity containing the <b>sim\_waiting</b> statement and that match the
predicate requirements.  It is normally used in clocked entities to look for packets sent by some other entity in the previous clock period. *E.g.* in

<tt> if  (sim\_waiting(ev, I\_Input) > 0)</tt>

where <b>I\_Input</b> is a predicate defined using

<tt> sim\_from\_port I\_Input(instr\_in);</tt>

the condition will be true if one or more packets is waiting at port <b>instr\_in</b>.

#### sim\_select

When an entity can have received packets on a number of ports since it processed the last one, <b>sim\_select</b> is used in conjunction with <b>sim\_waiting</b> to select the appropriate packet for a particular action.

##### Example

The following example is taken from the EMMA project:

```
  sim_from_port InputA(input1);
  sim_from_port InputB(input2);
  if  (sim_waiting(InputA) > 0)
    {
	  sim_select (InputA, ev);
	  SIM_CAST_DEL( int, inputA, ev);
	  mpx_input1 = inputA;
    }
  if  (sim_waiting(InputB) > 0)
    {
	  sim_select (InputB, ev);
	  SIM_CAST_DEL( int, inputB, ev);
	  mpx_input2 = inputB;
    }
```

Here the entity can receive packets on two ports, <b>input1</b> and <b>input2</b>, and uses a predicate for each to determine whether a packet has arrived. It uses <b>sim\_select</b> to select the appropriate packet and <b>SIM\_CAST\_DEL</b> (described below) to copy the content of the packet into a local variable (<b>inputA</b> or <b>inputB</b>).  <b>sim\_select</b> can also use the built-in predicate <b>SIM\_ANY</b>
which matches any event.

#### sim\_get\_next

<b>sim\_get\_next(ev)</b> can be used in entities in a clocked system that are not themselves clocked, *e.g.* the Registers entities in the DLX and MIPS projects, which are essentially slave entities to the Instruction Decode and Write Back entities. It takes the next event for the entity from the event queue and returns the packet content as <b>ev</b>.

##### Example

```
  sim_get_next(ev);
  if (ev.type()== SET_DEST_BUSY)
   {
    SIM_CAST( set_dest_busy_struct, set_dest_busy_pkt, ev);
    *ACTION 1*
   }
  else if (ev.from_port(from_instr_decode1))
   {
    *ACTION 2*
   }
  else if (ev.from_port(from_instr_decode2))
   {
    *ACTION 3*
   }
  else if (ev.from_port(from_write_back))
   {
    *ACTION 4*
   }
```

Here the next event can come via a <b>sim\_schedule</b> operation or from one of three ports.


#### sim\_get\_next\_before

<b>sim\_get\_next\_before(ev, timeout)</b> is similar to <b>sim\_get\_next(ev)</b> in that it takes the next event for the entity from the event queue and returns the packet content as <b>ev</b>, but only if the event occurs before a simulation time equal to <b>timeout</b> has elapsed.

### Timing

#### sim\_hold

<b>sim\_hold(delay)</b> holds the entity for <b>delay</b> simulation time units.

### Macro instructions

Macro instructions are used to marshal (<b>SIM\_PUT</b>) and unmarshal (<b>SIM\_CAST</b>) a packet associated with an event. <b>SIM\_PUT</b> is usually used before calling sim\_schedule while <b>SIM\_CAST</b> is used after <b>sim\_select</b>, <b>sim\_waiting</b>, <b>sim\_get\_next</b> or <b>sim\_get\_next\_before</b>.

#### SIM\_PUT(type,val)

Produces a packet with the C++ type <b>type</b> and the value <b>val</b>.

#### SIM\_CAST(type,var,ev)

Extracts the packet contained in the event <b>ev</b> assuming its C++ type is type. The value of this event is put in the C++ variable <b>var</b>. Obviously, the type of this variable must be compatible with <b>type</b>.

#### SIM\_CAST\_DEL(type,var,ev)

As for <b>SIM\_CAST</b> except that the entry on the event queue is deleted and cannot be re-accessed. This is useful in large projects where the amount of memory space used during runtime is an issue.

[<- Userguide](<Userguide.md>)

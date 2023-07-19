<a name="top"></a>
## Clocks

Most projects are simulations of sychronous systems in which the entities are clocked. HASE has a number of built-in clock mechanisms which can be invoked by (a) declaring the appropriate clock entity in the project definition file (b) using the EXTENDS option in the definition of an entity to link the entity to the clock.

A clock in HASE serve two purposes:

1. to simulate the clock in the real-life version of the system being simulated
2. to sychronise the simulation code of the entities against ticks of the clock, in order to ensure an orderly transfer of data between entities.


The entities required to support these mechanisms:

[Clock](<#clock>) &nbsp; &nbsp; &nbsp; [Clocked](<#clocked>) &nbsp; &nbsp; &nbsp; [Biclocked](<#biclocked>) &nbsp; &nbsp; &nbsp; [Pll](<#pll>)

are contained in the **sync** library.

<a name="clock"></a>
### Clock

The Clock entity operates in two phases:

1. Send a tick signal to any registered entity. The tick signal contains an integer value which represents the tick number.
2. Wait for completion signals from any of the registered entities. If the period time is reached and some entities have not sent their completion signals, then an error message is printed to inform the user that the Clock is too fast (Clock.period is too small).  
An integer value should be attached to the completion signal. If this value is negative, then the clock stops its behaviour assuming an error has occurred.  Otherwise, the cycle starts again at phase1.

Declaration: ENTITY Clock **sync** ()

#### Parameters:

- **int cycle** (read_only) - number of ticks which have actually been sent to the registered entities. For an example of how cycle can be used in the Hase++ behavioural code of an entity, see the [Entity Code Structure](<code.md>) section.

[<- top](<#top>)
<a name="clocked"></A>
### Clocked

Declaration: 

```
ENTITYLIB (
		   ABSTRACT Clocked sync ( )
		   ENTITY Clock sync ( )
		   ENTITY cpu (
						EXTENDS (Clocked)
					    *etc.*
		  );
```

Clocked defines the basic behaviour of a 1-phase synchronous entity which:

-  executes the **pre** service (defined in the **$pre** section of the .hase file
-  for any signal coming from clockName:Clock:
	-  executes the **tick** service defined in the **$tick** section of the .hase file).
 	-  sends a completion signal to clockName:Clock.
  		- The value sent with the completion signal is 1 as a default.
		- If the **stopSimulation()** method has been called, then the value is 0.
		- If the **stopSimulation(i)** method has been called then the value is *int* **i**.
		- When **i** is negative this means that an error has occurred and the entity wants the simulation to be stopped.

#### DebugMode:

The Clocked entity has a debug mode in which a statement is printed at the begin and the end of the tick service. Use the **bool debug;** attribute to set and reset the debug mode.

#### Parameters

- **String clockName** - name of an entity of type Clock with which the entity is to be  registered; normally only used when a particular instance of an entity is to be registered with a different clock (*e.g.* a [Pll](<#pll>) from that specifed in the entity type declarations, *e.g.*

```
AENTITY cpu CPU(
			    DESCRIPTION("CPU")
				ATTRIB(RPARAM(clockName,CPU_CLOCK))
			   )
```

[<-top](<#top>)
<a name="biclocked"></A>
### Biclocked

Declaration:

```
ENTITYLIB (
			ABSTRACT Biclocked sync ( )
			ENTITY Clock sync ( )
			ENTITY Clockphase sync ( )
ENTITY cpu (
			EXTENDS (Biclocked)
			*etc.*
		 );
```

Biclocked defines the basic behaviour of a 2-phase synchronous entity:

- The actual implementation of Biclocked is inherited from Clocked; it redefines the **tick(int no)** service:
-  When *no* is even, the service **phase0(int no)** defined in the $phase0 section of the .hase file is called with the value no/2.
-  When *no* is odd, the service **phase1(int no)** defined in the $phase1 section of the .hase file is called with the value no/2.

#### Debug mode:

The Biclocked entity has also a debug mode in which a statement is printed at the begin  and the end of each phase0/phase1 service. Use the **bool debug;** attribute to set and reset the debug mode.

**NB:** Only one service (either phase0 or phase1) is executed for each signal coming from the Clock entity.  This means that the overall period of a Biclocked entity is **twice** the period of the Clock.

#### Parameters

- **String clockName** - name of an entity of type Clock with which the entity is to be  registered; normally only used when a particular instance of an entity is to be registered with a different clock (*e.g.* [Pll](<#pll>)) from that specifed in the entity type declarations.

[<- top](<#top>)
<a name="pll"></a>
### Pll

The Pll entity acts in a similar manner to a phase lock loop, in that it is synchronised to a clock but runs at a different frequency. As a HASE entity it has the same behaviour as a Clock entity but is only activated when a tick signal is received from the entity with which it has registered (a Clock or another Pll).

### Syntax

```
ENTITY Pll sync ()
AENTITY Pll CPU_CLOCK(  
					  DESCRIPTION("CPU fast clock")
					  ATTRIB(RPARAM(ratio,4))
AENTITY cpu CPU(
					  DESCRIPTION("CPU")
 					  ATTRIB(RPARAM(clockName,CPU_CLOCK))
				  )
```

#### Parameters:

- *int* **ratio** - ratio by which the period of the incoming clock must be divided.

- *string* **clockName** - As a Clocked entity, a Pll can select the Clock (or any other entity inheriting from the Clock class *e.g.* another Pll) with which it wants to register.

- *int* **mainCycle** (read_only ) - number of ticks which have been sent by the Clock entity with which the Pll has registered.

-  *int* **pllCycle** (read_only) - number of ticks which have been sent to the registered entities.

[<- top](<#top>)

[<- Userguide](<Userguide.md>)

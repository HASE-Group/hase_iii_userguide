## RPARAM - Initialising Parameters

### Synopsis

Except for arrays, initial parameter values are set as part of the parameter declaration in the PARAMLIB or ENTITYLIB sections of the project definition files. However, if more than one copy of a particular entity is instantiated in the STRUCTURE section, it is sometimes convenient to initialise one or more of the entity's parameters differently for each instantiation.

The **RPARAM** construct allows this to be done. It is used within the (optional) ATTRIB section inside an AENTITY definition.

### Syntax

```
ATTRIB (RPARAM (parameter_name, parameter_value))
```

### Example:

The following definition fragments could be used in a multiprocessor system project where each processor's cache needs to include its own identifying number in packets sent to the interconnection network.

In <tt>ENTITYLIB</tt>:

```
ENTITY cache (
EXTENDS (Biclocked)
      			DESCRIPTION("cache1")
      			STATES (CACHE_IDLE, CACHE_BUSY)
	  			PARAMS (
              		RARRAY (cache_array, cache_mem, 256);
              		static RRANGE (cache_size, 1, 8, 3);
              		RINT (own_no, 0);
      			)
      			PORTS (
             			PORT (to_proc, proc_link, SOURCE);
             			PORT (from_proc, proc_link, DESTINATION);
             			PORT (to_network, net_link, SOURCE);
             			PORT (from_network, net_link, DESTINATION);
      			)
      	 		ATTRIB ()
     	 	 );
```

In <tt>STRUCTURE</tt>:

```
AENTITY cache CACHE1(
        DESCRIPTION("Cache 1")
        ATTRIB (RPARAM(own_no,1))
        )
AENTITY cache CACHE2(
        DESCRIPTION("Cache 2")
        ATTRIB (RPARAM(own_no,2))
        )
AENTITY cache CACHE3(
        DESCRIPTION("Cache 3")
        ATTRIB (RPARAM(own_no,3))
        )
AENTITY cache CACHE4(
        DESCRIPTION("Cache 4")
        ATTRIB (RPARAM(own_no,4))
        )
```

**NB** An alternative way of achieving the same effect is to declare <tt>own_no</tt> within *cache.hase* and use the following code in the <tt>$pre</tt> section to set its value.

```
  name = get_name();
  if (strcmp(name,"CACHE1")==0) {own_no=1;}
  if (strcmp(name,"CACHE2")==0) {own_no=2;}
  if (strcmp(name,"CACHE3")==0) {own_no=3;}
  if (strcmp(name,"CACHE4")==0) {own_no=4;}
```

In this case <tt>own_no</tt> is not displayed on-screen as a cache parameter and is not recorded in the trace file.

The **XPORT** construct (described in [Ports](<port.html>) is similar to RPARAM in that it enables different instances of an entity to have different numbers of ports.  
[<- Defining a Project](<project.md>)

[<- Userguide](<Userguide.md>)

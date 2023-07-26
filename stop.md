## Stopping a Simulation

The statement:    <tt>stopSimulation(0);</tt>

included in a .hase file will cause the simulation to stop normally. In a multiprocessor simulation, for example, this might be included in code such as:

```
  if (nodes_done==4)
   {stopSimulation(0);}
```

where nodes_done is a global variable which is incremented by each of the 4 nodes (processors) in the system once they have executed all their (simulated) instructions.

Error conditions may also be detected by behavioural code and used to stop a simulation, *e.g.*:

```
  if ((mem_address >= 0) && (mem_address < Mem_Size))
     {mem_output = instr_mem[mem_address];}
  else
      {stopSimulation(-i);}
```

where  **i** is an integer chosen to indicate that, in this case, an attempt is being made to access a non-existent memory address.  The value of  **i** will be reported in the Output panel.

Similarly, the following code:

```
$class_decls
  int cycle;
  Clock* MyClock;
  
$pre
  MyClock = (Clock*) sim.get_entity( sim.get_entity_id( "CLOCK" ) );
  
$phase0
  cycle = MyClock->cycle / 2; // assuming a biclocked entity
   if (cycle>=max_cycles)
     {stopSimulation(-j);}
```

where max_cycles is a parameter of the entity containing this code, could be used to stop runaway simulations going on for ever, a useful facility during project development.

[<- Userguide](<Userguide.md>)

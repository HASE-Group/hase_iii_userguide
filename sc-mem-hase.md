```
// Project:	Simple Processor
// Entity	Memory
// File:	memory.hase
// Date:	Dec 2005

// The Memory Unit contains the RARRAYs instr_mem and data_mem.
// Requests are processed in ClockPhase 0 and returned 
// in a subsequent ClockPhase 1.

$class_decls

  bool i_request, r_request, w_request;
  int mem_address;
  int dmem_output, write_data;

//structures and variables

   t_simple_instrn_set imem_output;
   t_i_mem_access I_Input;
   t_d_mem_access D_Input;

$pre

  i_request = false;
  r_request = false;
  w_request = false;
  mem_address = 0;
  my_state = M_IDLE;
  dump_state();

$phase0

// predicates for use in sim_wait tests

  sim_from_port inst_access(instr_request);	    
  sim_from_port data_access(data_request);	    

	 if (sim_waiting(ev, inst_access) > 0)
	  {
	   SIM_CAST_DEL(t_i_mem_access, I_Input, ev);
	   int mem_address = instr_mem.process(I_Input.address.label, I_Input.address.offset);

	   i_request = true;
	   my_state = M_BUSY;
	   dump_state();

	   if ((mem_address >= 0) && (mem_address < I_Mem_Size))
		 {imem_output = instr_mem[mem_address];}

	   else {stopSimulation(-1);}	// Invalid instruction memory address
	  }

	 if (sim_waiting(ev, data_access) > 0)
	  {
	   SIM_CAST_DEL(t_d_mem_access, D_Input, ev);
	   mem_address = D_Input.address;
	   if ((mem_address <= 0) && (mem_address > D_Mem_Size))
		{stopSimulation(-1);}	// Invalid data memory address
	   else 
		{
		 if (strcmp(D_Input.status,"r") == 0)
		  {
		   r_request = true;
		   dmem_output = data_mem[mem_address];
		  }
		 else if (strcmp(D_Input.status, "w") == 0)
		  {
		   w_request = true;
	           write_data = D_Input.data;
	 	   data_mem.Update(mem_address, write_data);
		  }
		   my_state = M_BUSY;
		   dump_state();
		}
	   }
 	// end of ClockPhase 0

$phase1

  if (i_request)
	{
	  send_INSTRUCTION(instr_out, imem_output);
	  my_state = M_IDLE;
	  dump_state();
	  i_request = false;
	}

  if (r_request)
	{
	 send_DATA(data_out, dmem_output);
	  my_state = M_IDLE;
	 r_request = false;
	}
  if (w_request)
	{
	 dmem_output = 0;
	 send_DATA(data_out, dmem_output);
	  my_state = M_IDLE;
	 w_request = false;
	}
  dump_state_i();
	// end of ClockPhase 1
```

```
// Project:	Simple Processor
// Entity	Processor
// File		proc.hase
// Date:	Dec 2005

$class_decls

//entity references

  Clock * myClock;

//structures and variables

  bool d_mem_access, i_mem_access;

  char Destination[8], Source1[8], Source2[8];
  char src1T, src2T, destT;

  int cycle;  
  int max_cycles;  
  int dest, src1, src2;				// register numbers 
  int src1_op, src2_op, dest_op;		// register operands	
  int literal, sign_bit, sign_ext;
  int mask;
  int mem_address, d_input;

  t_reg_args dest_args, src1_args, src2_args;	// structures for holding
						// decoded register arguments
  t_prog_reg update;
  t_simple_instrn_set i_input;
  t_i_mem_access imem_request;
  t_d_mem_access dmem_request;

//classes

$class_defs

$pre

// set up pointers to other entities

  myClock = (Clock*) sim.get_entity( sim.get_entity_id( "CLOCK" ) );

// set max_cycles to 2 ^ log_max_cycles (parameter value)

   max_cycles = 1 << log_max_cycles;

// preset current instruction to NOP

  IR.reset();		// sets IR = first instruction in declaration

// initialise PC, memory access flags, state

  strcpy(PC.label, "main");
  PC.offset = 0;
  i_mem_access = true;
  d_mem_access = false;
  my_state = PROC_BUSY;

  dump_state();

$phase0

  cycle = myClock->cycle / 2;

// predicates for use in sim_waiting test

  sim_from_port I_Input(instr_in);	    
  sim_from_port D_Input(data_in);	    

    // DEAL WITH DATA INPUT

  if  (sim_waiting(ev, D_Input) > 0)
       {
	 SIM_CAST_DEL(int, d_input, ev);
	 switch(IR.function)
	 {
	  case  LDM:
	  	strcpy(Destination, IR.loadi_field.dest_reg);
	  	dest_args = Decode_Register(Destination);
		destT  = dest_args.type;
	  	dest  = dest_args.number & 15;
		dest_op = d_input;
		if (destT == 'R')
		 {
		  update.reg_value = dest_op;
	 	  if (dest == 0)
	  	   {update.reg_value = 0;}
	  	  main_reg.Update(dest, update);
		  PC.offset ++;
		  i_mem_access = true;
		 }
		else {stopSimulation(-1);}
	   	break;
	  case STM:
		PC.offset ++;
		i_mem_access = true;
	   	break;
	  }
       }

    // DEAL WITH INSTRUCTION INPUT

  else if  (sim_waiting(ev, I_Input) > 0)
       {
	SIM_CAST_DEL(t_simple_instrn_set, i_input, ev);
	IR = i_input;
	sign_ext = 0;

	my_state = PROC_BUSY;

	switch(IR.function)
	 {
	  case STOP:
		my_state = PROC_IDLE;
		stopSimulation(0);
		break;
	  case JUMP:	// Jump to PC = literal
		literal = IR.immediate & 65535;
						// mask off ms 16 bits
		if ((literal & 32768) != 0)
			{sign_ext = -1 ^ 65535;} // generate sign extension
		literal = literal | sign_ext;
	 	strcpy(PC.label, "main");
		PC.offset = literal;
		i_mem_access = true;
   		break;
	  case JREG:	// Jump to PC = value in register
	  	strcpy(Source1, IR.src_reg);
	  	src1_args = Decode_Register(Source1);
		src1T  = src1_args.type;
	  	src1  = src1_args.number & 15;
		if (destT == 'R')
		 {
		  src1_op = main_reg[src1].reg_value;
	 	  strcpy(PC.label, "main");
		  PC.offset = src1_op;
		  i_mem_access = true;
		 }
		else {stopSimulation(-1);}
	   	break;
	  case BRANCH:	// Branch if CC = 1
		if (CC == 1)
		 {PC.offset = 0;
		  strcpy(PC.label, IR.label);}
		else
		 {PC.offset ++;}
		 i_mem_access = true;
		break;

	  case LDI:	// Load Register with Immediate
	  	strcpy(Destination, IR.loadi_field.dest_reg);
	  	dest_args = Decode_Register(Destination);
		destT  = dest_args.type;
	  	dest  = dest_args.number & 15;
		literal = IR.loadi_field.immediate & 65535;
						// mask off ms 16 bits
		if ((literal & 32768) != 0)
			{sign_ext = -1 ^ 65535;} // generate sign extension
		literal = literal | sign_ext;
		dest_op = literal;
		if (destT == 'R')
		 {
		  update.reg_value = dest_op;
	 	  if (dest == 0)
	  	   {update.reg_value = 0;}
	  	  main_reg.Update(dest, update);
		  PC.offset ++;
		  i_mem_access = true;
		 }
		else {stopSimulation(-1);}
	   	break;
	  case LDM:	// Load Register from memory[address + index]
		d_mem_access = true;
		strcpy(dmem_request.status,"r");
		strcpy(Source1, IR.load_field.src_reg);
	  	src1_args = Decode_Register(Source1);
		src1T  = src1_args.type;
	  	src1  = src1_args.number & 15;
		if (src1T == 'R')
		 {
		  src1_op = main_reg[src1].reg_value;
		  mem_address = src1_op + IR.load_field.address;
		  dmem_request.address = mem_address;
		  dmem_request.data = 0;
		 }
		else {stopSimulation(-1);}
	   	break;

	  case STM:	// Store Register value to memory[address + index]
		d_mem_access = true;
		strcpy(Source1, IR.store_field.src_reg);
	  	src1_args = Decode_Register(Source1);
		src1T  = src1_args.type;
	  	src1  = src1_args.number & 15;
		src1_op = main_reg[src1].reg_value;
		dmem_request.data = src1_op;
		strcpy(Source2, IR.store_field.dest_reg);
	  	src2_args = Decode_Register(Source2);
	  	src2  = src2_args.number & 15;
		src2_op = main_reg[src2].reg_value;
		if ((src1T == 'R') && (src2T == 'R'))
		 {
		  mem_address = src2_op + IR.store_field.address;
		  dmem_request.address = mem_address;
		 }
		else {stopSimulation(-1);}
	   	break;
	}	// end of switch

  if (IR.decode_ALU())
   {
  	strcpy(Destination, IR.alu_field.dest_reg);
  	strcpy(Source1, IR.alu_field.src_reg1);
  	strcpy(Source2, IR.alu_field.src_reg2);
  	src1_args = Decode_Register(Source1);
  	src1  = src1_args.number & 15;
	src1T  = src1_args.type;
  	src2_args = Decode_Register(Source2);
  	src2  = src2_args.number & 15;
	src2T  = src2_args.type;
  	dest_args = Decode_Register(Destination);
  	dest  = dest_args.number & 15;
	destT  = dest_args.type;
	if ((src1T == 'R') && (src2T == 'R') &&  (destT == 'R'))
	 {
	  src1_op = main_reg[src1].reg_value;
	  src2_op = main_reg[src2].reg_value;
	  switch (IR.function)
	   {
	    case ADD:	// ADD Rx Ry Rz: Rx = Ry + Rz
		dest_op = src1_op + src2_op;
	   	break;

	    case SUB:	// SUB Rx Ry Rz: Rx = Ry - Rz
		dest_op = src1_op - src2_op;
	   	break;

	    case MUL:	// MUL Rx Ry Rz: Rx = Ry & Rz
		dest_op = src1_op * src2_op;
	   	break;

	    case DIV:	// DIV Rx Ry Rz: Rx = Ry & Rz
		dest_op = src1_op / src2_op;
	   	break;

	    case AND:	// AND Rx Ry Rz: Rx = Ry & Rz
		dest_op = src1_op & src2_op;
	   	break;

	    case OR:	// OR Rx Ry Rz: Rx = Ry v Rz
		dest_op = src1_op | src2_op;
	   	break;

	    case XOR:	// XOR Rx Ry Rz: Rx = Ry ^ Rz
		dest_op = src1_op ^ src2_op;
	   	break;

	    case SLL:	// Shift Left Logical Rx Ry Rz: Rx = Ry < Rz
		dest_op = src1_op << src2_op;
	   	break;

	    case SRL:	// Shift Right Logical Rx Ry Rz: Rx = Ry > Rz
		dest_op = src1_op << src2_op;
		mask = -1 ^ ( ( (pow (2, src2_op)) - 1) << (32 - src2_op));
		dest_op = (src1_op >> src2_op) & mask;
	   	break;

	  case SRA:	// Shift Right Arithmetic Rx Ry Rz: Rx = Ry > Rz
		sign_bit = src1_op & (1 << 31);
		sign_ext = ( (pow (2, src2_op)) - 1) >> (32 - src2_op);
		dest_op = (src1_op >> src2_op);
			if (sign_bit != 0)
			{dest_op = dest_op | sign_ext;}
	   	break;

	   }	// end of switch
	  update.reg_value = dest_op;
	  if (dest == 0)
	   {update.reg_value = 0;}
	  main_reg.Update(dest, update);
	  PC.offset ++;
	  i_mem_access = true;
	 }	// end of if ((src1T ...))
	else {stopSimulation(-1);}

   } // end of if ( (IR.decode_ALU()) ...)

  if (IR.decode_ALUI())
   {
  	strcpy(Destination, IR.alui_field.dest_reg);
  	strcpy(Source1, IR.alui_field.src_reg1);
	literal = IR.alui_field.immediate & 65535;
					// mask off ms 16 bits
	if ((literal & 32768) != 0)
		{sign_ext = -1 ^ 65535;} // generate sign extension
	literal = literal | sign_ext;
	  	src1_args = Decode_Register(Source1);
  	src1  = src1_args.number & 15;
	src1T  = src1_args.type;
  	dest_args = Decode_Register(Destination);
  	dest  = dest_args.number & 15;
	destT  = dest_args.type;
	if ((src1T == 'R') && (destT == 'R'))
	 {
	  src1_op = main_reg[src1].reg_value;
	  switch (IR.function)
	   {

	    case ADDI:	// ADDL Rx Ry L: Rx = Ry + Literal
		dest_op = src1_op + literal;
	   	break;

	  case SUBI:	// SUB Rx Ry L: Rx = Ry - Literal
		  dest_op = src1_op - literal;
	   	break;

	  case MULI:	// MULL Rx Ry L: Rx = Ry * Literal
		  dest_op = src1_op * literal;
	   	break;

	  case DIVI:	// DIVL Rx Ry L: Rx = Ry / Literal
		  dest_op = src1_op / literal;
	   	break;

	  case ANDI:	// AND Rx Ry L: Rx = Ry & Literal
		  dest_op = src1_op & literal;
	   	break;

	  case ORI:	// OR Rx Ry L: Rx = Ry v Literal
		  dest_op = src1_op | literal;
	   	break;

	  case XORI:	// XOR Rx Ry L: Rx = Ry ^ Literal
		  dest_op = src1_op ^ literal;
	   	break;

	  case SLLI:	// Shift Left Logical Rx Ry Rz: Rx = Ry > Literal
		  dest_op = src1_op << literal;
	   	break;

	  case SRLI:	// Shift Right Logical Rx Ry L: Rx = Ry > Literal
		  mask = -1 ^ ( ( (pow (2, literal)) - 1) << (32 - literal));
		  dest_op = (src1_op >> literal) & mask;
	   	break;

	  case SRAI:	// Shift Right Arithmetic Rx Ry L: Rx = Ry > Literal
		  sign_bit = src1_op & (1 << 31);
		  sign_ext = ( (pow (2, literal)) - 1) >> (32 - literal);
		  dest_op = (src1_op >> literal);
			if (sign_bit != 0)
			{dest_op = dest_op | sign_ext;}
		break;

	}	// end of switch
		  update.reg_value = dest_op;
	 	  if (dest == 0)
	  	   {update.reg_value = 0;}
	  	  main_reg.Update(dest, update);
		  PC.offset ++;
		  i_mem_access = true;
	}	// end of if ((src1T ...))
		else {stopSimulation(-1);}
   }	// end of if ( (IR.decode_ALUL()) ... )

  if (IR.decode_SETCC() )
   {
  	strcpy(Source1, IR.comp_field.src_reg1);
  	strcpy(Source2, IR.comp_field.src_reg2);
  	src1_args = Decode_Register(Source1);
  	src1  = src1_args.number & 15;
	src1T  = src1_args.type;
  	src2_args = Decode_Register(Source2);
  	src2  = src2_args.number & 15;
	src2T  = src2_args.type;
	if ((src1T == 'R') && (src2T == 'R'))
	 {
	  src1_op = main_reg[src1].reg_value;
	  src2_op = main_reg[src2].reg_value;
	  CC = 0;	
	  switch (IR.function)
	   {
	    case SEQ:	// Set CC = 1 if RS1 = RS2
	 	  if (src1_op == src2_op)
	  	   {CC = 1;}
	   	break;

	    case SNE:	// Set CC = 1 if RS1 != RS2
	 	  if (src1_op != src2_op)
	  	   {CC = 1;}
	   	break;

	    case SGT:	// Set CC = 1 if RS1 > RS2
	 	  if (src1_op > src2_op)
	  	   {CC = 1;}
	   	break;

	    case SLT:	// Set CC = 1 if RS1 < RS2
	 	  if (src1_op < src2_op)
	  	   {CC = 1;}
	   	break;

	    case SGE:	// Set CC = 1 if RS1 >= RS2
	 	  if (src1_op >= src2_op)
	  	   {CC = 1;}
	   	break;

	    case SLE:	// Set CC = 1 if RS1 <= RS2
	 	  if (src1_op <= src2_op)
	  	   {CC = 1;}
	   	break;

	   }	// end of switch statement
	 	  PC.offset ++;
		  i_mem_access = true;
	}	// end of if (( src1T ...))
		else {stopSimulation(-1);}

   }	// end of SETCC instructions

	dump_state();
 
	} 		// end of code for new instruction pkt

	// end of  ClockPhase 0

$phase1

  if (cycle >= max_cycles) 	// stop simulation if max cycles exceeded
	{stopSimulation(0);}

  if (i_mem_access) {		// Send instruction request to memory
	imem_request.address.offset = PC.offset;
	strcpy(imem_request.address.label,PC.label);
	strcpy(imem_request.status,"i");
	send_I_MEM_ACCESS(instr_request, imem_request);
	i_mem_access = false;
	my_state = PROC_IDLE;
	dump_state();
	}

  if (d_mem_access) {		// Send data request to memory
	send_D_MEM_ACCESS(data_request, dmem_request);
	d_mem_access = false;
	}

	dump_state();

	// end of  ClockPhase 1
```
[<- Example Project](s-comp.md)

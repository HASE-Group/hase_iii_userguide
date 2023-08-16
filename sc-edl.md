```
PROJECT(
	PREAMBLE(
		 NAME "S-COMP"
		 AUTHOR "Roland Ibbett"
		 VERSION 1
		 DESCRIPTION "Simple example computer" 
		 )	
	PARAMLIB (

-- DEFINITION OF INSTRUCTION STRUCTURES

-- operands for loading a register from memory using address + (index reg)
	STRUCT (t_load, [RSTRING (dest_reg, "-", 5), RSTRING (src_reg, "-", 5),
RINT (address, 0)]);

-- operands for storing a register to memory using address + (index reg)
	STRUCT (t_store, [RSTRING (src_reg, "-", 5), RSTRING (dest_reg, "-", 5), RINT (address, 0)]);

-- operand for loading register with immediate
	STRUCT (t_loadi, [RSTRING (dest_reg, "-", 5), RINT (immediate, 0)]);

-- operands for move instruction
	STRUCT (t_move, [RSTRING (dest_reg, "-", 5), RSTRING (src_reg, "-", 5)]);
-- operands for ALU scalar instruction group
	STRUCT (t_alu,  [RSTRING (dest_reg, "-", 5),
			 RSTRING (src_reg1, "-", 5),
			 RSTRING (src_reg2, "-", 5)]);

--  operands for ALUI scalar instruction group
	STRUCT (t_alui,  [RSTRING (dest_reg, "-", 5),
			 RSTRING (src_reg1, "-", 5),
			 RINT (immediate, 0)]);

-- operands for COMP instruction
	STRUCT (t_comp, [RSTRING (src_reg1, "-", 5), RSTRING (src_reg2, "-", 5)]);

-- Definition of instruction set
	INSTR (t_simple_instrn_set, [(NOP),
			(JUMP, RINT (immediate, 0)),
			(JREG, RSTRING (src_reg,  "-")),
			(SETCC(SEQ,SNE,SGT,SLT,SGE,SLE), RSTRUCT (t_comp, comp_field)),
			(BRANCH, RSTRING (label, "-", 50)),
			(LDM, RSTRUCT (t_load,  load_field)),
			(LDI, RSTRUCT (t_loadi, loadi_field)),
			(STM, RSTRUCT (t_store, store_field)),
			(ALU(ADD,SUB,MUL,DIV,AND,OR,XOR,SLL,SRL,SRA), 
			  RSTRUCT (t_alu, alu_field)),
			(ALUI(ADDI,SUBI,MULI,DIVI,ANDI,ORI,XORI,SLLI,SRLI,SRAI),			  RSTRUCT (t_alui,  alui_field)),
			(STOP) ], function);


-- definition of memory access packet (data)
	STRUCT (t_d_mem_access, [RSTRING(status, "-"),
				 RINT(address, 0),
				 RINT(data, 0)]);

-- definition of program counter
	STRUCT (pc_struct, [RSTRING(label,"main"), RINT(offset, 0)]);

-- definition of memory access packet (instruction)
	STRUCT (t_i_mem_access, [RSTRING(status, "-"),
				 RSTRUCT(pc_struct, address)]);

-- definition of instruction memory array
	ARRAYI (instr_mem_array, t_simple_instrn_set);

-- definition of data memory array
	ARRAY (data_mem_array, int);

-- definition of programmable register
	STRUCT (t_prog_reg, [RINT(reg_value, 0)]);

-- definition of programmable register set
	ARRAY (prog_reg_set, t_prog_reg);

-- definition of links to memory
	LINK (l_to_dmem,[(D_MEM_ACCESS,RSTRUCT(t_d_mem_access,DP))]);
	LINK (l_to_imem,[(I_MEM_ACCESS,RSTRUCT(t_i_mem_access,DP))]);


-- definition of data link
	LINK (l_data,[(DATA,RINT(value,0))]);

-- definition of instruction links
	LINK(l_instr,[(INSTRUCTION,RINSTR(t_simple_instrn_set, instrn))]);
	)

	GLOBALS (
	)

	ENTITYLIB (

		ABSTRACT Biclocked sync ( )
		ABSTRACT BiclockedD sync ( )
		ENTITY Clock sync ( )
		ENTITY Clockphase sync ( )

	ENTITY proc (
		EXTENDS (Biclocked)
		DESCRIPTION ("Array Control Unit")
		STATES (PROC_IDLE, PROC_BUSY)
		PARAMS (
			RRANGE (log_max_cycles, 1, 20, 8)
			RARRAY ( prog_reg_set, main_reg, 16);
			RINSTR (t_simple_instrn_set, IR);
			RSTRUCT ( pc_struct, PC);
			RINT (CC,0 );
			)
		PORTS (
			PORT(instr_request,l_to_imem,SOURCE);
			PORT(instr_in,l_instr,DESTINATION);
			PORT(data_request,l_to_dmem,SOURCE);
			PORT(data_in,l_data,DESTINATION);
			)
		    );

	ENTITY memory (
		EXTENDS (Biclocked)
		DESCRIPTION ("Memory")
		STATES (M_IDLE, M_BUSY)
		PARAMS (
			RINT (I_Mem_Size, 65536);
			RARRAY ( instr_mem_array, instr_mem, I_Mem_Size);
			RINT (D_Mem_Size, 65536);
			RARRAY ( data_mem_array, data_mem, D_Mem_Size);
		)
	  	PORTS (
			PORT(instr_request,l_to_imem,DESTINATION);
			PORT(instr_out,l_instr,SOURCE);
			PORT(data_out,l_data,SOURCE);
			PORT(data_request,l_to_dmem,DESTINATION);
	  		)
		    );
	)

	STRUCTURE(
	
	AENTITY Clock CLOCK ( DESCRIPTION ("Clock"));

	AENTITY Clockphase CPHASE (DESCRIPTION ("Clock display"));

	AENTITY proc PROC ( DESCRIPTION ("Array Control Unit"));

	AENTITY memory MEMORY ( DESCRIPTION ("CU Memory"));

	CLINK(proc.PROC[instr_request]->memory.MEMORY[instr_request],1);
	CLINK(proc.PROC[data_request]->memory.MEMORY[data_request],1);
	CLINK(memory.MEMORY[instr_out]->proc.PROC[instr_in],1);
	CLINK(memory.MEMORY[data_out]->proc.PROC[data_in],1);
	)
)
```
[<- Example Project](s-comp.md)

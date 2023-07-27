```
// Project:	Simple Processor
// Entity	Global Functions
// File		globals_fns.h
// Date:	Dec 2005

class Clock;    // Units are declared here to allow forward references
class acu;      // between them. HASE automatically declares them from
class memory;   // the .edl file, but in sequence, only allowing
                // backward references

int pow(int, int);  // forms exponential without using doubles

float floatFromInt(int v);
int floatToInt(float d);

struct t_reg_args { 
  char type;       // Should only be R
  int  number;     // Register number
  int  index;	   // Offset (if any)
};

class t_reg_args Decode_Register(char *Instruction);
```


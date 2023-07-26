## Global Functions

It is often convenient to have a number of functions available to multiple entities, *e.g.* instruction decoding functions. This can be achieved using the global function files:

*global_fns.h*

containing the function declarations, *e.g.*

<tt>int Decode_DBL(t\_dlx\_instrn\_set Instruction);</tt>

*global_fns.cpp*

containing C++ code for the functions, *e.g.*

```
int Decode_DBL(t_dlx_instrn_set Instruction)
{
      if ( Instruction.function == t_dlx_instrn_set::MOVD
      ||Instruction.function == t_dlx_instrn_set::JAL
      ||Instruction.function == t_dlx_instrn_set::JALR
      )
    return 1;
  return 0;
}
```

*global_fns.h* can also be used to overcome problems caused by forward referencing during compilation, *i.e.* when the code for one entity refers to a variable in another entity that appears later in the project's entity library. By declaring each entity as a class in the *global_fns.h* file, the order of declaration in the entity library becomes irrelevant, *e.g.* for a pipelined processor, the class declarations might be

```
class registers;
class memory;
class instr_fetch;
class instr_decode;
class execute;
class mem_access;
class write_back;
```


HASE automatically includes and compiles the global_fns files if they exist.

[<- Userguide](<Userguide.md>)

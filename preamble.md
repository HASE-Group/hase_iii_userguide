## PREAMBLE

The **PREAMBLE** section of the project definition specifies the basic attributes of the project, for example its name, working directory and author.

<pre>preamble ::= PREAMBLE ( { preamble_item } )

preamble_item ::=
      NAME "preamble_name"
    | LIBRARY preamble_library
    | AUTHOR "preamble_author"
    | VERSION preamble_version
    | DESCRIPTION "preamble_description"
</pre>

### preamble_name

*str* - The name of the project.

### preamble_library

*str* - optional - Additional options to link external libraries.  While **extern** is used to link external .o files, **library** can be used to link libraries (.a, .so) (*e.g.* "-ldistribj -L${HASEDIR}/distributions").

### preamble_author

*str* - optional - The name of the author of the project.

### preamble_version

<pre>preamble_version ::=
      natural . natural
    | natural
</pre>  

The current version of the project.

### preamble_description

*str* - optional - A string describing an aspect of the project.

### Example

<pre>
PREAMBLE (
    NAME "EMMA-3"
    AUTHOR "Roland Ibbett"
    VERSION 3.1
    DESCRIPTION "Edinburgh Microcoded Microprocessor"
)
</pre>

[<- Defining a Project](<project.md>)

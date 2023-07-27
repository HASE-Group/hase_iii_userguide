```
title : position (0,10)
CLOCK : position (20,270)
CPHASE : position (20,270)
CPHASE : STATES S:ClockS P0:ClockP0 P1:ClockP1
CPHASE : PARAM cycle MODE VALUE POSITION (70,32)

PROC : position (20,160)
PROC : STATES PROC_IDLE:proc_idle PROC_BUSY:proc_busy
PROC : PARAM PC MODE VALUE POSITION (200,2)
PROC : PARAM CC MODE VALUE POSITION (200,22)
PROC : PARAM IR MODE VALUE POSITION (200,42)
PROC : PORT instr_request side TOP position 30
PROC : PORT data_request side TOP position 30
PROC : port instr_in side TOP position 110 ICON portd
PROC : PORT data_in side TOP position 110 ICON portd

MEMORY : position (20,50)
MEMORY : STATES M_IDLE:memory_idle M_BUSY:memory_busy
MEMORY : port instr_request side BOTTOM position 30 ICON portu
MEMORY : port data_request side BOTTOM position 30 ICON portu
MEMORY : port instr_out side BOTTOM position 110
MEMORY : port data_out side BOTTOM position 110
```


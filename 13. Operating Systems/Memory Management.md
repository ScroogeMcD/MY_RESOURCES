# Memory Management

## Basic Hardware
Main memory and the registers built into the processor itself are the only general-purpose storage that the CPU can access directly. There are machine instructions 
that take memory addresses as arguments, but none that take disk addresses as arguments. Therefore any instructions in execution, and any data being used by the 
instructions, must be in one of these direct-access storage devices. If the data are not in memory, they must be moved there before the CPU can operate on them.

## Logical vs Physical Address Space
* **Logical address** : An address generated by the CPU is referred to as Logical or Virtual Address.
* **Physical address** : The address seen by the memory unit - i.e. the one loaded into the memory-address reigster of the memory, is commonly refereed to as physical address.

In compile-time and load-time address-binding methods of a program, identical logical and physical addresses are generated. However execution-time address binding scheme results in differnt logical
and physical address. The run-time mapping from virtual to physical addresses is done by a hardware device called the **memory-management unit (MMU)**

## Memory-mapping files

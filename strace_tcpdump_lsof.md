### System Call
A system call is a userspace request of a kernel service. SystemCalls are an API provided to user-space by the kernel.
System memory (i.e. Virtual Memory and not physical RAM) in linux can be divided into two regions : Kernel Space and User Space.   
Kernel space can be accessed by user processes only through the use of system calls.

One of the most common system-call is **execve(const char *pathname, char *const argv[], char *const envp[])**.   
It executes the program referred to by pathname. This causes the program that is currently being run by the calling process, to be replaced with a new program, with newly initialized stack, heap, and (initialized and uninitialized) data segments.

### strace


### tcpdump


### lsof

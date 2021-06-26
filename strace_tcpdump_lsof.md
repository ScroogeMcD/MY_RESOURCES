### System Call
A system call is a userspace request of a kernel service. SystemCalls are an API provided to user-space by the kernel.
System memory (i.e. Virtual Memory and not physical RAM) in linux can be divided into two regions : Kernel Space and User Space.   
Kernel space can be accessed by user processes only through the use of system calls.

One of the most common system-call is **execve(const char *pathname, char *const argv[], char *const envp[])**.   
It executes the program referred to by pathname. This causes the program that is currently being run by the calling process, to be replaced with a new program, with newly initialized stack, heap, and (initialized and uninitialized) data segments.

### strace

It provides details of the system calls being invoked in the system. In OS X, strace is not present. Instead we can use **dtrace** in the following way :
```sudo dtrace -n 'syscall:::entry /pid == 89348/ {@[probefunc] = count();}'```
![Screenshot 2021-06-26 at 3 54 46 PM](https://user-images.githubusercontent.com/13499858/123510063-0196d500-d697-11eb-9a48-0636e2ed9ab5.png)




### tcpdump


### lsof

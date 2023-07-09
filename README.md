# Memory_manager
#This is a project for the CTU course on Operating Systems, written in C++.

#The practice statement is as follows:

"This task is focused on the memory allocation problem that is done by the OS and the runtime of user programs. The problem is to implement a simplified version of a memory manager.

We assume a program where the dynamic memory allocator does not exist (it is disabled in the testing Progtest environment). Thus, there is no malloc, free, ..., nor C++ operators new or delete. Your task is to implement variants of the functions.

Your implementation will be given a continuous memory pool of a given size. The memory pool will be sized between 1MiB and several hundreds MiB. Your functions will manage the memory. That is, the rest of the program may ask your function to allocate some block of the memory and subsequently the block may be released. Your implementation must keep track of the allocated/free regions, must support allocation/releasing of a block, and joining of freed adjacent blocks.

The implementation consists of the following functions:

int HeapInit ( void * startAddr, int size );
Function HeapInit will be invoked first. The parameters are: pointer startAddr that points to the beginning of the managed memory pool and size which is the size (in bytes) of the managed memory pool.

The passed memory pool will be used to simulate the heap. The subsequent requests shall allocate memory from that pool. Moreover, you shall use the passed memory pool to store your service information (e.g. the information which blocks were allocated/freed).

void HeapDone ( int * pendingBlk );
Function HeapDone, will be called when the heap is to be destroyed. The function will report a summary on how many blocks were left unfreed in the managed memory pool. The number of unfreed blocks is passed via output parameter pendingBlk.

void * HeapAlloc ( int size );
Function HeapAlloc is used to allocate a memory block on the heap. The use is similar to malloc. The parameter is the size of the memory block to allocate (in bytes), the return value is a pointer to the beginning of the allocated contiguous block of size at least size. If the function fails (e.g. there is no memory left or all available blocks are smaller than the required size), the return value shall be NULL.

bool HeapFree ( void * blk );
Function HeapFree is used to free previously allocated memory block. The parameter is a pointer to the memory block as it was returned by HeapAlloc. The function returns either true (success), or false if it fails to free the block (the pointer is invalid, block has been already freed, ...).

Submit a source file with the implementation of the four required functions, your auxiliary functions, data types and declarations. Use the example below as a basis for your implementation. If the conditional compile directives are left unchanged, the source can be used to test the program locally as well as the source may be submitted to the Progtest.

Your implementation is significantly limited (see the list of available header files). There is no STL, no C dynamic allocation function (malloc, ...) and no C++ memory allocation operators (new, ...). The program will be executed in a limited testing environment. There is a strict limit on the available memory. In addition to the managed memory pool, there is only a few kilobytes available for your data segment and stack. This extra memory is small, it is not big enough to store the required service information (you must keep the service information inside the managed memory pool).

There are mandatory and optional tests in this homework. The mandatory tests check whether your implementation correctly allocates the memory blocks (blocks do not overlap, freed adjacent blocks are joined, ...).

Optional tests check the handling of fragmentation and the speed of the implementation. If these tests are not passed, some points will be awarded, however, there will be a penalty."

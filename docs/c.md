## Pointers
### Reference Operator
In expressions `*` derefences a pointer, (and does other jobs as per grammar).

In declarations `*` marks a variable as a pointer.

!!! important
    The rule of declaration in C is, you declare it the way you use it.

    !!! example
        `int *p` means you need `*p` (a pointer) to get a `int`

        `int **p` means you need `**p` (a pointer to a pointer) to get a `int`

        `int ***p` means you need `***p` (a pointer to a pointer to a pointer) to get a `int`

=== "Example"
    ```c title="Little Examples of Pointers" linenums="1"

    #include <stdio.h>

    void printppp(int ***ptr) {
        printf("pointer to pointer to pointer: %d\n", ***ptr);
    }

    int main(void) {
        // i has the value 1
        int i = 1;
        // k is a special variable where it just points to i
        int *p = &i;
        // p is a special variable where it just points to k
        int **pp = &p;
        // ppp is a special variable where it just points to pp
        int ***ppp = &pp;

        printf("value: %d\n", i);
        printf("pointer: %d\n", *p); 
        // since p points to k, which points to i, we need to deference p twice
        printf("pointer to pointer: %d\n", **pp); // or *(*p)
        // since the function accepts a pointer, we don't deference it
        printppp(ppp);
       	return 0;
    }
    ```

=== "Output"
    ```
    value: 1
    pointer: 1
    pointer to pointer: 1
    pointer to pointer to pointer: 1
    ```
### Address Operator
In expressions `&` gets you the address of a pointer.

In declarations,

```c linenums="1"
void somefunc(int a)
```

Creates a copy of `a`

```c linenums="1"
void somefunc(int *a)
```

Takes a pointer to an `int` as input. But makes a copy of the pointer.

### Constant Pointers
Consider the following example,
```c linenums="1"
const int *ptr = &a;
```
Here, `ptr` points to `a` which is of `const int` type. What that means is that the value of `a` is read-only; _however_ that does not mean the pointer is read-only: the reference that `ptr` holds can be changed at any time,
```c linenums="1"
ptr = &b;
```
... Is perfectly legal.

However if a constant pointer is declared, its value cannot be changed:
```c linenums="1"
const int *const ptr = &a;
```
=== "Example I"
    ```c linenums="1"
    #include <stdio.h>

    int main(void) {
        const int a = 69;
        const int b = 420;
    	const int *ptr = &a;

    	printf("ptr: %d\n", *ptr);
    	ptr = &b;
    	printf("ptr: %d\n", *ptr);

        return 0;
    }
    ```
=== "Output"
    ```
    ./main
    ptr: 69
    ptr: 420
    ```
=== "Example II"
    ```c linenums="1"
    #include <stdio.h>

    int main(void) {
        const int a = 69;
        const int b = 420;
    	const int *const ptr = &a;

    	printf("ptr: %d\n", *ptr);
    	ptr = &b;

        return 0;
    }
    ```
=== "Output"
    ```
    main.c:9:6: error: cannot assign to variable 'ptr' with const-qualified type 'const int *const'
        9 |         ptr = &b;
          |         ~~~ ^
    main.c:6:19: note: variable 'ptr' declared const here
        6 |         const int *const ptr = &a;
          |         ~~~~~~~~~~~~~~~~~^~~~~~~~
    1 error generated.
    ```
    
    As you can see, a constant pointer's value cannot be changed.

### Arithmetic
!!! important
    When you increment a pointer of any type by `n`, it increments the pointer by the size of its type times `n`.
#### Simple
=== "Example I"
    ```c linenums="1"
    #include <stdio.h>
    #include <inttypes.h>

    int main(void) {
        int a = 69;
        int *p = &a;

        printf("sizeof(int):  %ld\n", sizeof(int));
        printf("Address of p: %lu\n", (uintptr_t)p);

        long unsigned n1 = (uintptr_t)++p;
        long unsigned n2 = (uintptr_t)++p;

        printf("n1:           %lu\n", n1);
        printf("n2:           %lu\n", n2);
        printf("Difference:   %lu, %lu\n", n2 - n1, (uintptr_t)p - n1);
        return 0;
    }
    ```
=== "Output"
    ```	
    ./main
    sizeof(int):  4
    Address of p: 140721500910884
    n1:           140721500910888
    n2:           140721500910892
    Difference:   4, 4
    ```
=== "Example II"
	```c linenums="1"
    #include <inttypes.h>
    #include <stdio.h>

    int main(void)
    {
        const int M = 3;
        const int N = 6;

        int a = 69;
        int *p = &a;

        printf("sizeof(int):  %ld\n", sizeof(int));
        printf("Address of p: %lu\n", (uintptr_t)p);

        long unsigned n1 = (uintptr_t)(p + M);
        long unsigned n2 = (uintptr_t)(p + N);

        printf("n1:           %lu\n", n1);
        printf("n2:           %lu\n", n2);
        printf("Difference:   %lu, %lu\n", n2 - n1, n2 - (uintptr_t)p);
        printf("%d * %d = %d, %d * %d = %d\n", sizeof(int), M, sizeof(int) * M, sizeof(int), N, sizeof(int) * N);
        return 0;
    }
    ```
=== "Output"
	```
    ./main
    sizeof(int):  4
    Address of p: 140721262450916
    n1:           140721262450928
    n2:           140721262450940
    Difference:   12, 24
    4 * 3 = 12, 4 * 6 = 24
    ```
#### Arrays
Since arrays are contiguous in memory, you can do quite a bit of sophisticated array
manipulation using pointer arithmetic.
=== "Example I"
	```c linenums="1"
    #include <stdio.h>

    void printp(int *i) {
        printf("%d\n", *i);
    }


    int main(void)
    {
        int t[5] = {1,2,3,4,5};
        int *d = &t[0];
        for (int i = 0; i < 5; i++) {
            printp(d+i);
        }
        return 0;
    }
	```
=== "Output"
	```
    ./main
    1
    2
    3
    4
    5
	```
=== "Example II"
	```c linenums="1"
	
    #include <stdio.h>

    void printp(int i) {
        printf("%d\n", i);
    }


    int main(void)
    {
        int t[5] = {1,2,3,4,5};
        int *d = &t[0];
        for (int i = 0; i < 5; i++) {
            printp(*(d++));
        }
        return 0;
    }
	```
=== "Output"
	```
    ./main
    1
    2
    3
    4
    5
	```
!!! note
	`*(p+n)` is equivalent to `p[n]`. The latter is a syntactic sugar for the former.

=== "Example I"
    ```c linenums="1"
    #include <stdio.h>

    int main() {
        int arr[] = {1,2,3,4};
        for (int i = 0; i < 4; i++) {
            printf("%p %d\n", arr+i, *(arr+i));
        }

        return 0;
    }
    ```
=== "Output"
	```
    ./main
    0x7ffd9a93ad90 1
    0x7ffd9a93ad94 2
    0x7ffd9a93ad98 3
    0x7ffd9a93ad9c 4
	```

=== "Example II"
	```c linenums="1"
    #include <stdio.h>

    int main() {
        char text[] = "Test";
        // char text[] = {'T', 'e', 's', 't', '\0'};
        // char text[4] = {'T', 'e', 's', 't', '\0'};
        for (int i = 0; i < (sizeof(text) / sizeof(char) - 1); i++) {
            printf("%c", *(text+i));
        }
        printf("\n");

        return 0;
    }
	```
=== "Output"
	```
    ./main
    Test

	```
=== "Example III"
	```c linenums="1"
    #include <stdio.h>

    int main() {
        int i = 69;
        int* t[1][1] = {{&i}};
        printf("%d\n", ***t); 

        return 0;
    }
	```
=== "Output"
	```
	./main
	69
	```
### Structs
My theory is that structs in C are namespaces. I can't be sure of course. When you
declare a variable of a certain struct you are defining the variables in that struct under that
variable, so the variable itself is now just a container or namespace.

The part to note is that when you are declaring a variable as of certain struct type, you
are also declaring the variables within that struct type under that variable (the
variable is now just a namespace), and these variables are indeed instantiated (think of
handling arrays).

=== "Example I"
    ```c linenums="1"
    #include <stdio.h>

    typedef struct {
        const int height;
        const int width;
        const char *name[50];
    } Image;

    void print_image_ptr(const Image *image) {
        printf("height: %d, width: %d, name: %s\n", image->height, image->width, *image->name);
       }

    void print_image(const Image image) {
        printf("height: %d, width: %d, name: %s\n", image.height, image.width, *image.name);
    }

    int main() {
        const char name[50] = "Hello!";
        Image image = {1080, 720, name}; // (1)
        Image *image_ptr = &image;

        printf("height: %d, width: %d, name: %s\n", image.height, image.width, *image.name);
        printf("height: %d, width: %d, name: %s\n", image_ptr->height, image_ptr->width, *image_ptr->name);

        print_image_ptr(image_ptr);
        print_image(image);

        return 0;
    }
    ```
    {.annotate}
    
    1. 	Since `name` is an array, when you pass it to the constructor, the compiler
    passes a pointer to the first element of the array. If the struct wasn't accepting a
    pointer to an array of array of 50 `char`s, but instead accepted a pointer to an array
    of 50 `char`s then you would accept `const char* image` in the struct definition, for
    the same reason I just stated.
=== "Output"
    ```
    ./main
    height: 1080, width: 720, name: Hello!
    height: 1080, width: 720, name: Hello!
    height: 1080, width: 720, name: Hello!
    height: 1080, width: 720, name: Hello!
    ```
### Functions
You declare function pointers with the following syntax:
```
<return type> (*<pointer_name>)(<argument type>, ...);
```
=== "Example I"
	```c linenums="1"
	#include <stdio.h>

    int add(const int a, const int b) {
        return a + b;
    }

    int main(void) {
        int (*ptr_name)(const int, const int);

        ptr_name = add;
        ptr_name = &add;
        ptr_name = ******************add;

    	printf("Sum of 1 + 2 is %d\n", (*ptr_name)(1, 2));

        return 0;
    }
	```
=== "Output"
	```
    ./main
    Sum of 1 + 2 is 3
	```
## Dynamic Memory
`malloc`, `calloc`, `realloc`, `free`, are available in `stdlib.h`.

```c linenums="1"
void *calloc(size_t nmemb, size_t size);
void *malloc(size_t size);
void *realloc(void *ptr, size_t size);
void free(void *ptr);
```

- `calloc()` allocates memory for an array of `nmemb` elements
of size bytes each and returns a pointer to the allo-
cated memory. The memory is set to zero.

- `malloc()` allocates size bytes and returns a pointer to
the allocated memory. The memory is not cleared.

- `free()` frees the memory space pointed to by `ptr`, which
must have been returned by a previous call to `malloc()`,
`calloc()` or `realloc()`. Otherwise, or if `free(ptr)` has
already been called before, undefined behaviour occurs.
If `ptr` is `NULL`, no operation is performed.

- `realloc()` changes the size of the memory block pointed
to by `ptr` to size bytes. The contents will be unchanged
to the minimum of the old and new sizes; newly allocated
memory will be uninitialized. If `ptr` is `NULL`, the call
is equivalent to `malloc(size)`; if size is equal to zero,
the call is equivalent to `free(ptr)`. Unless `ptr` is
`NULL`, it must have been returned by an earlier call to
`malloc()`, `calloc()` or `realloc()`.

=== "Example I"
    ```c linenums="1"
    #include <stdio.h>
    #include <stdlib.h>

    void print_pointers(char *str, int size) {
        for (int i = 0; i < size; i++)
            printf("pointer: %s\n", str[i] == NULL ? "allocated" : "not allocated");
    }

    int main(void) {
        const int SIZE;

        printf("[PROMPT] Enter text size in bytes: ");
        scanf("%d", (int *) &SIZE);
        printf("[INFO] Entered bytes: %d\n", SIZE);

        char *str = (char *)malloc(SIZE * sizeof(char));

        print_pointers(str, SIZE);

        free(str);
        printf("[INFO] Memory free'd.\n");

        print_pointers(str, SIZE);

        return 0;
    }
    ```
    The first call to `print_pointer` will only print "allocated", but with the second
    call [the behaviour is undefined](https://en.wikipedia.org/wiki/Undefined_behavior).
=== "Output"
    ```
    ./main
    [PROMPT] Enter text size in bytes: 5
    [INFO] Entered bytes: 5
    pointer: 0
    pointer: 0
    pointer: 0
    pointer: 0
    pointer: 0
    [INFO] Memory free'd.
    pointer: -92
    pointer: 17
    pointer: 0
    pointer: 0
    pointer: 0   
    ```

!!! important
	```c linenums="1"
	int a[N];
	```
	```c linenums="1"
	int *p = (int) calloc(N * sizeof(int));
	```
    Here, `p` and `a` are _not_ equivalent. The first example requests a contiguous
    block of memory of size `N * sizeof(int)` then names it `a`, however `a` is not a
    pointer, it is an array. But the second example only assigns a reference to a contiguous
    block of memory to a pointer (`p`). When you pass `a` to a function the compiler instead
    passes a pointer to the first element of the array, but when you pass `p` to a function,
    it just passes itself. If a function happens to change the reference in the pointer `p`,
    the contiguous block of memory that it holds reference to is now forever lost. But in
    case of `a`, the original array is never lost since `a` is not a pointer by itself, it
    is the compiler that does the magic when you pass the array `a` to a function.

    !!! tip
        Please go through [C-FAQ 6.2](https://www.c-faq.com/aryptr/aryptr2.html) and [C-FAQ
        6.3](https://www.c-faq.com/aryptr/aryptrequiv.html).

!!! important
    Calling `free()` on a pointer doesn't change it, only marks memory as free. Your
    pointer will still point to the same location which will contain the same value, but
    that value can now get overwritten at any time, so you should never use a pointer after
    it is `free`'d. To ensure that, it is a good idea to always set the pointer to `NULL`
    after `free`'ing it.

### Memory Leaks
Consider the following example,
```c linenums="1"
#include <stdio.h>
#include <stdlib.h>


int main(void) {
    int N = 30;
    int A[N];

    for (int i = 0; i < N; i++)
        A[i] = random();

    int *B = malloc(2 * N);
    B = A;

    return 0;
}
```
Here we declare a static array of `N=30`, then we assign random values to the pointers. 
We then allocate `N*2` bytes to `B` pointer. We then overwrite the reference value in 
variable `B` with `A`'s reference value. Now the dynamic memory block's reference we got 
from `malloc` at line 12 is lost. This loss of bytes is termed as a memory leak.

!!! tip
	See this [forum
    post](https://stackoverflow.com/questions/5134891/how-do-i-use-valgrind-to-find-memory-leaks)
    to find memory leaks in your program.

### Copying Memory
- The  `memcpy()`  function  copies `n` bytes from memory area `src` to memory area
`dest`.  The memory areas must not overlap.
- The  `memmove()`  function  copies `n` bytes from memory area `src` to memory area
`dest`. The memory areas may overlap: copying takes place as though the bytes in
`src` are first copied into a temporary array that does not overlap `src` or `dest`, and the
bytes are then copied from the temporary array to `dest`. 
```c linenums="1"
void *memcpy(void *dest, const void *src, size_t n);
void *memmove(void *dest, const void *src, size_t n);
```
`memcpy()` and `memmove()` are declared in `string.h`. `memcpy()` does not support overlapping 
but `memmove()` does. That's the difference between the two. Otherwise they are equivalent.
Look and understand the example below and then study the output.

=== "Example I"
	```c linenums="1"
    #include <stdio.h>
    #include <stdlib.h>
    #include <string.h>

    void print_arr(const int *arr, const int size, const int h_at) {
        printf("{");
        for (int i = 0; i < size; i++) {
            if (h_at == i) printf("[");
            printf("%d", arr[i]);
            if (h_at == i) printf("]");
            if (i < (size - 1)) printf(", ");
        }
        printf("}\n");
    }

    int main(void) {
        int A[] = {1, 2, 3, 4};
        int B[] = {5, 6, 7, 8};

        const int A_SIZE = sizeof(A) / sizeof(int);
        const int B_SIZE = sizeof(B) / sizeof(int);

        int *con_arr = calloc(A_SIZE + B_SIZE, sizeof(int));
        memcpy(con_arr, A, A_SIZE * sizeof(int));
        memcpy(con_arr + A_SIZE, B, B_SIZE * sizeof(int));

        int *con_arr_copy = calloc(A_SIZE + B_SIZE, sizeof(int));
        memcpy(con_arr_copy, con_arr, (A_SIZE + B_SIZE) * sizeof(int));

        printf("Original:    ");
        print_arr(con_arr, A_SIZE + B_SIZE, -1);
        printf("\n");

        for (int offset = 0; offset <= A_SIZE; offset++) {
            memcpy(con_arr + A_SIZE - offset, con_arr, A_SIZE * sizeof(int));
            printf("[%d] memcpy:  ", offset);
            print_arr(con_arr, A_SIZE + B_SIZE, A_SIZE - offset);
            memcpy(con_arr, con_arr_copy, sizeof(con_arr) * sizeof(int));
        }

        printf("\n");

        for (int offset = 0; offset <= A_SIZE; offset++) {
            memmove(con_arr + A_SIZE - offset, con_arr, A_SIZE * sizeof(int));
            printf("[%d] memmove: ", offset);
            print_arr(con_arr, A_SIZE + B_SIZE, A_SIZE - offset);
            memcpy(con_arr, con_arr_copy, sizeof(con_arr) * sizeof(int));
        }

        return 0;
    }
	```
=== "Output"
	``` linenums="1"
    ./main
    Original:    {1, 2, 3, 4, 5, 6, 7, 8}

    [0] memcpy:  {1, 2, 3, 4, [1], 2, 3, 4}
    [1] memcpy:  {1, 2, 3, [1], 2, 3, 1, 8}
    [2] memcpy:  {1, 2, [1], 2, 1, 2, 7, 8}
    [3] memcpy:  {1, [1], 2, 2, 4, 6, 7, 8}
    [4] memcpy:  {[1], 2, 3, 4, 5, 6, 7, 8}

    [0] memmove: {1, 2, 3, 4, [1], 2, 3, 4}
    [1] memmove: {1, 2, 3, [1], 2, 3, 4, 8}
    [2] memmove: {1, 2, [1], 2, 3, 4, 7, 8}
    [3] memmove: {1, [1], 2, 3, 4, 6, 7, 8}
    [4] memmove: {[1], 2, 3, 4, 5, 6, 7, 8}
	```
### Stack and Heap
#### Stack
<figure markdown="span">
    ![](https://files.catbox.moe/vk72gb.png#only-light)
    ![](https://files.catbox.moe/eqyilp.png#only-dark)

	<figcaption>Memory organization of a typical program in
    [MIPS](https://en.wikipedia.org/wiki/MIPS_architecture)</figcaption>
</figure>

Areas of memory of a program are called segments: the text segment, the stack segment,
and the heap segment.

- The **text** (or code) segment contains the compiled code of the executable.
- The **stack** segment is used to store your local variables and is used for passing
arguments to the functions along with the return address of the instruction which is to
be executed after the function call is over. When a new stack frame needs to be added
(as a result of a newly called function), the stack grows downward.
- The **heap** segment is used when program allocate memory at runtime using `calloc`
and `malloc` function, then memory gets allocated in heap. When some more memory need to
be allocated using `calloc` and `malloc` function, heap grows upward as shown in above
diagram.

The stack and heap are traditionally located at opposite ends of the process’s virtual
address space. The stack grows automatically when accessed, up to a size set by the
kernel (which can be adjusted with `setrlimit(RLIMIT_STACK, ...)` on UNIX systems). The
heap grows when the memory allocator invokes the `brk()` or `sbrk()` system call,
mapping more pages of physical memory into the process’s virtual address space.

Implementation of both the stack and heap is usually down to the runtime/OS. Often games
and other applications that are performance critical create their own memory solutions
that grab a large chunk of memory from the heap and then dish it out internally to avoid
relying on the OS for memory

Stacks in computing architectures are regions of memory where data is added or removed in a [last-in-first-out](https://en.wikipedia.org/wiki/FIFO_(computing_and_electronics))
manner. In most modern computer systems, each thread has a reserved region of memory referred to as
its stack. When a function executes, it may add some of its state data to the top of the stack; when the
function exits it is responsible for removing that data from the stack. At a minimum, a thread’s stack is used
to store the location of function calls in order to allow return statements to return to the correct location,
but programmers may further choose to explicitly use the stack. If a region of memory lies on the thread’s
stack, that memory is said to have been allocated on the stack.

Because the data is added and removed in a last-in-first-out manner, stack allocation is very simple and
typically faster than heap-based memory allocation (also known as dynamic memory allocation). Another
feature is that memory on the stack is automatically, and very efficiently, reclaimed when the function exits.

!!! important
	Do not return pointers to static variables from a function's scope. Static variables are
    automatically `free`'d outside the function's scope.

!!! tip
	For a simplified introduction to stack and heap read
    [this](https://courses.grainger.illinois.edu/cs225/fa2022/resources/stack-heap/) and for
    more detailed introduction read
    [this](https://cs.gmu.edu/~zduric/cs262/Slides/teoX.pdf). 
- The OS allocates the stack for each system-level thread when the thread is created. Typically the OS
is called by the language runtime to allocate the heap for the application.
- The stack is attached to a thread, so when the thread exits the stack is reclaimed. The heap is typically
allocated at application startup by the runtime, and is reclaimed when the application (technically
process) exits.
- The size of the stack is set when a thread is created.
- You would use the stack if you know exactly how much data you need to allocate before compile time
and it is not too big.
- The stack is faster because the access pattern makes it trivial to allocate memory from it, while the
heap has much more complex bookkeeping involved in an allocation or free. Also, each byte in the
stack tends to be reused very frequently which means it tends to be mapped to the processor’s cache,
making it very fast.
- Variables created on the stack will go out of scope and automatically deallocate.
- Much faster to allocate in comparison to variables on the heap.
- Implemented with an actual stack data structure.
- Stores local data, return addresses, used for parameter passing
- Can have a stack overflow when too much of the stack is used. (mostly from inifinite or too much
recursion and very large allocations)
- Data created on the stack can be used without pointers.
- In C you can get the benefit of variable length allocation through the use of `alloca()`, which allocates on
the stack, as opposed to alloc, which allocates on the heap. This memory won’t survive your return
statement, but it’s useful for a scratch buffer.
#### Stack Overflow
Variables created on the stack are always contiguous with each other, writing out of bounds can
change the value of another variable.

=== "An Instance of Stack Overflow"
    ```c linenums="1"
    #include <string.h>


    void foo (char *bar) {
        char c[12];
        strcpy(c, bar); // no bounds checking...
    }

    int main (int argc, char **argv) {
        foo(argv[1]);
    }
    ```
    When an argument larger than 11 bytes is suplied on the command line `foo()` overwrites
    local stack data, the saved frame pointer, and most importantly, the return address.
    When `foo()` returns it pops the return address off the stack and jumps to that address
    (i.e. starts executing instructions from that address). In the example above,
    the attacker has overwritten the return address with a pointer to the stack
    buffer char `c[12]`, which now contains attacker supplied data. In an actual stack
    buffer overflow exploit the string of ”A”’s would be replaced with
    [shellcode](https://en.wikipedia.org/wiki/Shellcode) suitable to the platform.
=== "Before"
    ![](https://files.catbox.moe/0gsm5s.png)
=== "After"
    ![](https://files.catbox.moe/1lebjb.png)

#### Heap

The heap contains a linked list of used and free blocks. New allocations on the heap (by
 `new` (C++) or `malloc()`) are satisfied by creating a suitable block from one of the
free blocks. This requires  updating list of blocks on the heap. This meta information
about the blocks on the heap is also stored on the heap  often in a small area just in
front of every block.

- The size of the heap is set on application startup, but can grow as space is needed
(the allocator requests more memory from the operating system).
- Stored in computer RAM like the stack.
- Variables on the heap must be destroyed manually and never fall out of scope. The data is freed with
delete, delete[] or free
- Slower to allocate in comparison to variables on the stack.
- Used on demand to allocate a block of data for use by the program.
- Can have fragmentation when there are a lot of allocations and deallocations
- Can have allocation failures if too big of a buffer is requested to be allocated.
- You would use the heap if you don’t know exactly how much data you will need at runtime or if you
need to allocate a lot of data.
- Responsible for memory leaks.

#### Heap Overflow
A heap overflow, heap overrun, or heap smashing is a type of buffer overflow that occurs
in the heap data area. Heap overflows are exploitable in a different manner to that of
stack-based overflows. Memory on the heap is dynamically allocated at runtime and
typically contains program data. Exploitation is performed by corrupting this data in
specific ways to cause the application to overwrite internal structures such as linked
list pointers. The canonical heap overflow technique overwrites dynamic memory
allocation linkage (such as `malloc` metadata) and uses the resulting pointer exchange
to overwrite a program function pointer.

!!! tip
	Read more [here](https://en.wikipedia.org/wiki/Heap_overflow).

## Compilation

![](https://files.catbox.moe/my37na.png#only-light)
![](https://files.catbox.moe/nosp9f.png#only-dark)

!!! tip
	Refer to [this](https://people.sc.fsu.edu/~jburkardt/classes/isc_2012/c_program_compilation.pdf)
    and [this](https://galileo.phys.virginia.edu/compfac/courses/geek-hours/compilers-and-libs.html) for more in-depth information.

Libraries are archives of [object files](https://en.wikipedia.org/wiki/Object_file). In a
sense object files are simply compiled byte code of your source code that is yet to be
linked. Before linking your source code went through several stages, those stages as part of
their process ensure your linker can do its job correctly and efficiently. Before linking
your compiler only makes reference to things like external functions like `printf`. A
linker's job is to actually look for them and... Link them to your program.

Once the linker is done with its job, it outputs an executable that you can run on a
specific CPU architecture.

!!! note
	Inclusion of source code into another file using `#include`, `#define`, etc.,
    and making sure it actually exists is not the job of the linker. That's the job of the
    preprocessor. E.g., if your source code is missing the definition of a
    variable, the error is from the preprocessor, not the linker.

```
gcc -c main.c
```
Output an object file `main.o`.

```
gcc -S main.c
```
Output the assembly file `main.s`.

```
gcc -o main.c
```
Output an executable `main`.

## Libraries
As explained, libraries are archives of object files.
### Making and Using Static Libraries
For a bare-minimum example, you will need two files, `test.h` and `test.o`, to create the
static library. `test.h` is needed for the program that is to make use of your library; without
it the preprocessor will not be able to make sense of your use of symbols that are
undeclared from the preprocessor's perspective (not the linker).

To create the static library we will use [`ar`](https://en.wikipedia.org/wiki/Ar_(Unix))
that is available on Unix and Unix-like systems. This command is simply an archiver.

```
ar -csr libtest.a test.o
```

- `-c` create the library if it doesn't exist.
- `-s` generate an index.
- `-r` replace anything of the same name that is already in the library.

!!! tip
	You can view the manpage for more information, `man ar`.

!!! tip
	You can view the filenames of the object files that are in an archive with
	`ar -t libname.[a,so]`.
!!! note
    `gcc` lets you add other directories onto the linker's
    search path by defining the environment variable `LIBRARY_PATH`.  Just
    put a colon-separated list of directories into this variable, and `gcc`
    will add these directories to the standard list of places where it
    looks for static libraries.

A library must be prefixed with `lib`. A static library must end with `.a` extension.
Between `lib` and `.a` is the name of your library.

```
gcc -o main -L. -ltest main.c
```

- `-L` tells `gcc` to look in an additional directory when trying to find libraries.
- `-I` tells `gcc` to look in an additional directory when trying to find `include`
files.
- `-l` says to link the program with the following library. `test` is the name of the
library.

=== "test.h"
	```c linenums="1"
	int add(const int a, const int b);
	```
=== "test.c"
	```c linenums="1"
	#include "test.h"

	int add(const int a, const int b) {
		return a + b;
	}
	```
=== "main.c"
    ```c linenums="1"
    #include <stdio.h>
    #include "test.h"

    int main() {
    	printf("%d\n", add(1, 2));
    	return 0;
    }
    ```
    ``` title="Output of main.c"
    ./main
    3
    ```
### Making and Using Dynamic Libraries
Please read the Creating and Using Dynamic Libraries section from
[here](https://galileo.phys.virginia.edu/compfac/courses/geek-hours/compilers-and-libs.html).

## Type Qualifiers
!!! tip
	Refer to [this article
	](https://webhome.phy.duke.edu/~rgb/General/c_book/c_book/chapter8/const_and_volat
	ile.html) for more information on `const` and `volatile`.

### `const` (C89)
`const` is used with a datatype declaration or definition to specify an unchanging value.

```c linenums="1"
const int five = 5;
const double pi = 3.141593;
```

`const` objects may not be changed.

```c title="Illegal Uses of Const" linenums="1"
const int five = 5;
const double pi = 3.141593;

pi = 3.2;
five = 6;
```

### `volatile` (C89)

`volatile` specifies a variable whose value may be changed by processes outside the current program

=== "Example I"
    ```c title="Volatile Object That Might Be a Buffer Used to Exchange Data With an External Device" linenums="1"
    int check_iobuf(void) {
    	volatile int iobuf;
    	int val;

    	while (iobuf == 0) {
    	}
    	val = iobuf;
    	iobuf = 0;
    	return(val);
    }
    ```
    if `iobuf` had not been declared `volatile`, the compiler would notice that nothing happens inside the loop and thus eliminate the loop
    `const` and `volatile` can be used together.

	!!! note
        An input-only buffer for an external device could be declared as `const volatile` (or `volatile const`, order is not important) to make sure the compiler knows that the variable should not be changed (because it is input-only) and that its value may be altered by processes other than the current program

### `restrict` (C99)
`restrict` keyword _hints_ the compiler no other pointer can be used to point to the 
object that a pointer with this type qualifier points to.

!!! tip
	Read this [page](https://en.wikipedia.org/wiki/Restrict) for more inforamtion.

### `_Atomic` (C11)
Their purpose is to ensure race-free access to variables that are shared between
different threads. Without atomic qualification, the state of a shared variable would be
undefined if two threads access it concurrently.

!!! note
	For more information please refer to [this](https://stackoverflow.com/a/36955947) answer.

=== "Example I"
	```c title="Atomic Constant Integer Variable Declaration" linenums="1"
	_Atomic const int name;
	```

!!! tip
	Read about data races [here](https://stackoverflow.com/a/34550).

## Storage Classes
!!! tip
	Refer to [this page](
	https://sceweb.sce.uhcl.edu/helm/WEBPAGE-Cpp/my_files/TableContents/Module-10/module10page.html)
    for more information. You can also find more information on [this thread](
    https://stackoverflow.com/questions/3684450/what-is-the-difference-between-static-and-extern-in-c).

### `auto`
`auto` is the default storage class for all local variables. Automatic, or local, or stack variables only last for its scope's lifetime.
=== "Example I"
    ```c linenums="1"

    #include <stdio.h>
    #include <stdlib.h>

    int* ret_ptr(void) {
        auto int local_var = 69;
        int *local_var_ptr = &local_var;

        printf("ptr: %d\n", *local_var_ptr);

        return local_var_ptr;
    }

    int main(void) {
        int *local_var_ptr = ret_ptr();

        int *ints = alloca(sizeof(int) * 5);
        for (int i = 0; i < 5; i++) {
            ints[i] = random();
        }
       
    	printf("ptr: %d\n", *local_var_ptr);

        return 0;
    }
    ```
=== "Output"
	```
    ./main
    ptr: 69
    ptr: 1957747793
	```
### `static` (Internal Linkage)
The `static` storage class instructs the compiler to keep a local variable in existence
during the life-time of the program instead of creating and destroying it each time it
comes into and goes out of scope. Therefore, making local variables `static` allows them
to maintain their values between function calls.

!!! note
	`static` variables are by default initiliazed to zero. Non-static variables may also be
    zero but you are entirely dependent on the compiler's implementation (undefined behavior).

!!! note
    When `static` is explicitly used for declaring a global variable (or function), it causes that
    variable's scope to be restricted to the file in which it is declared.

=== "Example 1"
	```c linenums="1"
	#include <stdio.h>
    #include <stdlib.h>

    int* ret_ptr(void) {
        static int local_var = 69;
        int *local_var_ptr = &local_var;

        printf("ptr: %d\n", *local_var_ptr);

        return local_var_ptr;
    }

    int main(void) {
        int *local_var_ptr = ret_ptr();

        int *ints = alloca(sizeof(int) * 100);
        for (int i = 0; i < 100; i++) {
            ints[i] = random();
        }
       
    	printf("ptr: %d\n", *local_var_ptr);

        return 0;
    }
    ```
=== "Output"
	```
    ./main
    ptr: 69
    ptr: 69
	```
=== "Example II"
	```c linenums="1"
    #include <stdio.h>

    static int count = 10;

    void func(void) {
        static int i = 5;
        i++;
        printf("i is %d and count is %d\n", i, count);
    }

    int main(void) {
        while (count--) {
            func();
        }

        return 0;
    }
	```
=== "Output"
	```
    ./main
    i is 6 and count is 9
    i is 7 and count is 8
    i is 8 and count is 7
    i is 9 and count is 6
    i is 10 and count is 5
    i is 11 and count is 4
    i is 12 and count is 3
    i is 13 and count is 2
    i is 14 and count is 1
    i is 15 and count is 0
    ```

### `extern` (External Linkage)
The `extern` storage class is used to give a reference of a global variable that is
visible to ALL the program files. When you use `extern` the variable cannot be
initialized as all it does is point the variable name at a storage location that has
been previously defined.

!!! inforamtion
	When global variables (or functions) are declared, unless specified, they are classified with external linkage.

=== "main.c"
	```c linenums="1"
    #include <stdio.h>

    int count;
    extern void write_extern();
     
    int main() {
       count = 5;
       write_extern();
       return 0;
    }
    ```
=== "support.c"
	```c linenums="1"
    #include <stdio.h>

    extern int count;

    void write_extern(void) {
   		printf("count is %d\n", count);
    }
    ```
=== "Output"
	```
	> gcc support.c main.c -o main
	> ./main
	5
	```
### `register`
`register` keyword is used to suggest the compiler to put the variable in a CPU
register. Since CPU registers is the fastest memory (and very scarce in quantity),
it is recommended to be reserved for variables that are very frequently accessed.

!!! tip
	Read [this](https://gcc.gnu.org/onlinedocs/gcc/Global-Register-Variables.html) to know about global register variables.

!!! important
	`register` keyword merely reports a suggestion to the compiler; the compiler is within its legal rights to just ignore it if deemed unnecessary.

!!! note
	`register` variables don't have memory addresses since they are not in memory, so you are prohibited from using the `&` operator on such variables.

```c title="How to Register Variables" linenums="1"
register int name;
```

## Inline Functions
`inline` specifier hints the compiler to put the body of a function in its parent scope when it is called; thus avoiding placing data on a new stack frame and retrieving its data. It is merely a hint, the compiler is free to do what is best for _actual_ performance gains.

!!! tip
	Read more about inline functions [here](https://en.cppreference.com/w/c/language/inline).

A `static inline` function can be declared and defined with no restrictions, but there are restrictions for non-static inline functions. Read the citation in the tip for more information.

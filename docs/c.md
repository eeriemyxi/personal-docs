# Pointers
## Reference Operator
In expressions `*` derefences a pointer, (and does other jobs as per syntax).

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
## Address Operator
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

## Arithmetic
!!! important
    When you increment a pointer of any type by `n`, it increments the pointer by the size of its type times `n`.
### Simple
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
        int a = 69;
        int *p = &a;
        int M, N;
        M = 3;
        N = 6;

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
### Arrays
Since array contiguous in memory, you can do quite a bit of sophisticated array
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
!!! important
	```c linenums="1"
	int a[N];
	```
	is equivalent to,
	```c linenums="1"
	const int *a;
	```
	`*a` is a pointer to the first element of the array.
!!! note
	Since arrays are basically another way of allocating pointers in a contiguous block of
    memory, `*(p+n)` is equivalent to `p[n]`.

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
## Structs
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
    
    1. 	Since `name` is an array, when you pass it to the constructor, it is already an
    array of 50 pointers to the first element of the array. If the struct wasn't accepting a pointer
    to an array of array of 50 `char`s, but instead accepted a pointer to an array of 50 `char`s then
    you would accept `const char* image` in the struct definition, for the same reason I just stated.
=== "Output"
    ```
    ./main
    height: 1080, width: 720, name: Hello!
    height: 1080, width: 720, name: Hello!
    height: 1080, width: 720, name: Hello!
    height: 1080, width: 720, name: Hello!
    ```
## Dynamic Memory
`malloc`, `calloc`, `realloc`, `free`, are available in `stdlib.h`.

```c
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
    Calling `free()` on a pointer doesn't change it, only marks memory as free. Your
    pointer will still point to the same location which will contain the same value, but 
    that value can now get overwritten at any time, so you should never use a pointer after 
    it is `free`'d. To ensure that, it is a good idea to always set the pointer to `NULL` after
    `free`'ing it.

## Memory Leaks
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
variable `B` with `A`'s reference value. Now the dynamic memory block we got from
`malloc` at line 12 is lost. This loss of bytes is termed as a memory leak.

!!! tip
	See this [forum
    post](https://stackoverflow.com/questions/5134891/how-do-i-use-valgrind-to-find-memory-leaks)
    to find memory leaks in your program.

## Copying Memory
- The  `memcpy()`  function  copies `n` bytes from memory area `src` to memory area
`dest`.  The memory areas must not overlap.
- The  `memmove()`  function  copies `n` bytes from memory area `src` to memory area
`dest`. The memory areas may overlap: copying takes place as though the bytes in
`src` are first copied into a temporary array that does not overlap `src` or `dest`, and the
bytes are then copied from the temporary array to `dest`. 
```c
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

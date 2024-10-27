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
<!--TODO: add docs for struct and -> operator-->

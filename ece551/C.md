## C

Seven steps of writing a program:

1.  work an example
2.  write down
3.  generalize the steps
4.  test the algorithm
5.  translate into code
6.  test program
7.  debug program

### Compiling and Running

Compiler: translating the human-readable code into a machine-executable format. Run _gcc myFile.c_ to compile and produce an executable program called _a.out_. Then run _./a.out_ to execute. The process of compiling:

*   _preprocessor_:   Combine C source file with any _header files_ it includes, as well as expanding any _macros_ that used   Head file includes _function prototypes, macro definitions and type declaration_
    *   Function prototypes: provides an existing function’s return and parameter types for compiler to check, but it has a semicolon in place of the body.
    *   Macro definitions: Advantages: easy to modify and read, also being portability   ex: _#define EXIT_SUCCESS._   ex: _#define SQUARE(x)   x * x_ the preprocessor will pass what in the parentheses directly to the formula since there is no restriction on the type, but if we pass a _(y - z)_, we should rewrite the formula as _((x) * (x))_ in order to make things work. Also, if we pass in a function _f(i)_, _f(i)_ will be executed twice
    *   Type declaration: details about different types
*   _assembly_: parse and type-checking the program, then translate the code from preprocessor to a lowest-level type of human-readable code
*   _assemble into an object file_: the object file contains the machine-executable instructions for the source file
*   _link_: combine object files, various libraries and startup code to produce the actual executable binary

### Fixing Your Code: Testing and Debugging

While for design methodology, we apply the _top-down_ design, _incremental_ is naturally a _bottom-up_ approach. **Black Box Testing**: consider only the expected behavior of the function **White Box Testing**: examines the code to devise test cases   three types of test coverage

*   _statement coverage_: every statement in the function is executed
*   _decision coverage_: all possible outcomes of decisions are exercised. Using _control flow graph(CFG)_ to promise each arrow is covered
*   _path coverage_: span all possible valid paths through CFG. The number of paths through CFG is exponential in the number of conditional choices **Asserts**: check the end result and invariants Breakpoint: instruct the debugger to stop the execution of your program whenever the execution arrow is on the particular line. WatchPoint: stop when a particular “box” changes.

### Recursion

Head recursion: perform a computation after they make a recursive call Tail recursion: the recursion called is the last thing the function does before returning Tail call: a function caller returns immediately after the called returns Mutual recursion:a pair of recursions which call each other

### Pointer

The address of a variable is itself which could not be changed by the programmer. The _&_ symbol is a unary operand and is used before the lvalue whose value should be taken. _*_ is a unary operand that dereferences a pointer which gives the value of it. If _p_ is a pointer, then _*p_ is a lvalue.

The high-level depiction of a program’s memory layout:
![](https://github.com/YatingLi98/notes/raw/master/ece551/layout.png =300x300)
![](https://github.com/YatingLi98/notes/raw/master/ece551/example_of_memory.png =300x300)
The **stack** stores the local variables declared by each function. The stack is divided into stack frames which start when the function is called and last until it returns. The **return address** is the address of the instruction that should be executed after the function being called completes and returns.

**NULL**: a flat-headed arrow. By not having any valid portion of the program placed at address 0, we can be sure that nothing will be placed at address 0\. This means that no properly initialized pointer that actually points to something would ever have the value NULL. The attempt of following the arrow will lead to crash with a _segmentation fault_ (attempt to access memory in an invalid way).

**Pointers of Struct**:

*   $*a.b \Longleftrightarrow *(a.b)$: b is a pointer
*   $(*c).d \Longleftrightarrow c-{>}d$: c is a struct pointer

**const**

*   `const int x = 3` assign to x is illegal
*   `const int * p = &x OR int const * p = &x` assign to x is illegal while compiler is allowed to change where p points
*   `int * const p = &x` p is a const pointer to a modifiable int. *p = 4 is ok but p = &y is illegal
*   `const int * const p = &x` compiler can not change both

ADD CODE

We declare that x is a modifiable int and p is a readable pointer which can’t be used to change the value it points at.

ADD CODE

We declare that y is a constant, which means &y has the type const int *. However, assign &y to q which could be used to change *p. Thus, it causes an error. When a box has multiple names, we could say one name **aliases** another. **memory checker tools**: Valgrind and/or -fsanitize=address

### Arrays

*   `int myArray[4];` _myArray_ is a pointer points to the first box in the array instead of lvalue. The size can also be a previously declared variable. Three cases that an array can be used without array-to-pointer decay occuring:
    *   _myArray_ and _&myArray_ evaluate to the same numerical value but with different types. _myArray_ will evaluate to a pointer to an int while _&myArray_ will evaluate to a pointer to 4 ints
    *   sizeof myArray = 4 * (sizeof int). _sizeof_ is an operator and is evaluate at compile time
    *   use array to initialize strings
*   `int myArray[4] = {1}` if you write too few elements, the compiler will fill the remaining ones with 0.
*   `int myArray[] = {1, 2}` compile will figure out

**Dangling Pointers**: a pointer points to something whose memory has been deallocated. When we want to return an array, the storage spaces created for the array lives in the stack frame and thus are deallocated after the function returns.

**size_t**: unsigned int with the right number of bits to describe the size or index of an array

### Uses of Pointers

#### Strings

A string is a sequence of characters, terminated by the _null terminator_ character , ‘\0’.

**Declaration**:

*   `const char * str = "Hello World\n";` String literal is stored in the read-only portion of the static data section. The attempt to modifying it will lead to a _segmentation fault_.
*   If we want to declare a modifiable string: `char str[] = "Hello World\n";`. Note that the length is 13 including the null terminator. If we forget the place for null terminator, there may be error when we pass it to a string library.

**Equality**:

*   str1 == str2 check if str1 and str2 point at the same place
*   _strcmp_ a function in _string.h_ returns 0 if equal, a positive if the first is greater. The comparison is case-sensitive, which means _a_ is different from _A_
*   _strcasecmp_ the same with _strcmp_ except that it performs insensitive comparison

**Copy**:

*   _strncpy_: takes a parameter _n_ which tells the maximum number of characters it is allowed to take when copy. Note that the destination is necessarily null-terminated.
*   _strcpy_: if insufficient space is available, _strcpy_ will overwrite whatever follows.
*   _strdup_: allocates space for a copy and copies it into that space.

**Converting from Strings to ints**:

*   _atoi_: interpret the sequence of characters as a decimal number. If there is no valid number at the start, it returns 0.
*   _strtol_: specify the base and pass in the address of a char *, which it will fill in with a pointer to the first character after the number. Ex, “123xyz”, it will set the pointer to point at x. You can also pass in NULL.

#### Multidimensional Arrays

*   `int myMatrix[][2] = {{1, 2}};` When initialize a multidimensional arrays, we are only allowed to elide the first dimension’s size specification.
*   `int * myMatrix[2] = {array1, array2};`

Advantage of the second:

1.  each row can be different size
2.  _myMatrix[i]_ is a modifiable lvalue: because _myMatrix[i]_ is just a position obtained by calculation instead of getting an actual box for it
3.  elements can alia each other

#### Function Pointers

A pointer to the function itself. Just as the name of array, _&myFunction_ is the same as _myFunction_ because the name of function is already a pointer.

ADD CODE
//traditional way of declaration;
//* can be omitted//use typedef to define;
//a pointer points to an unspecified type of
//data which allows qsort to take an array of
//the number of elements in the array
//specify the size of each element for qsort
//to tell where one element ends and the next
//the parameters point to two elements to be
//example for int


#### Security Hazards

-Buffer overflow: write a string into a array that is too small-overflowing the array and writing over other data

-Format string attacks:

### Interacting with the User and System

###Dynamic Allocation
Dynamic memory alloction allows a programmer to request a specfic amount memory to be allocated on the **heap**, so it willed not be freed when the function returns (as those in stack). The upper boundary of the heap can increase if there is not enough free blocks of memory.

'#include <stdlib.h>'
#####malloc
`void * malloc (size_t size);`
Do not write a specific number of bytes:
- portability, the ability to compile and work on a different system
- maintainability
If there is not emough space to fulfill the request, _malloc_will return _NULL_. It is better to check it before using.
**calloc**: zeroes out the region in memory while _malloc_ does nothing to initialize the memory.
Only copy the pointer, two pointers point at the same location.
```C
polygon_t *p2 = p1;
```

Copy each field of *p1 into *p2, if *p1 has a array, then *p2 will have a array points at the same location. If we free *p1->points, then *p2->points will become a dangling pointer.
```C
polygon_t *p2 = malloc(sizeof(*p2));
*p2 = *p1;
```

Deep copy
```C
polygon_t * p2 = malloc(sizeof(*p2));
p2->num_points = p1->num_points;
p2->points = malloc(p2->num_points * sizeof(*p2->points));
for (int i = 0; i < p1->num_points; i++) {
  p2->points[i] = p1->points[i];
}
```
![](https://github.com/YatingLi98/notes/raw/master/ece551/copy_pointer.png =200x200)
![](https://github.com/YatingLi98/notes/raw/master/ece551/shallow_copy.png =200x200)
![](https://github.com/YatingLi98/notes/raw/master/ece551/deep_copy.png =200x200)

#####free
`void free(void * ptr);`
- _ptr_ could only be pointers that were returned by malloc
- a block of memory can't be freed twice
- can't free memory not in the heap

If _ptr_ is _NULL_, then nothing happens.
**Memory leaks**: lose all references to a block of memory, and the memory still allocated.

#####realloc
`void * realloc(void * ptr, size_t size)`

#####getline
`ssize_t getline(char ** line_write_into, size_t * size_malloc, FILE * f);`
Read a single line from the _f_ until it sees '\n', then place a '\0'. It returns _-1_ on an error(including end of error), and the number of bytes read on success. If _*line-write-into_ is _NULL_, it will perform a _malloc_.

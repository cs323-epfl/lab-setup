# The C Language (323 Abridged)

## Introduction

This is an abridged C language book (Can we really call it a book when it's in a markdown file?), focusing on aspects of C that can be explained quickly. While the
K&R "The C Programming Language" book is great, it's more than 155 pages for a language with similar syntax to Java for basic things and doesn't
really give tips and tricks on useful techniques that could make your life a lot easier. It's still recommended that you go through the K&R book if you have time since it
goes into much more detail.

# The Basics

We'll be going through this fairly quickly since most of these are things you should already know or will be familiar with from other languages.

## The `main()` Function

```C
int main(int argc, char **argv){
    return 0;
}
```

No more `public static void` or `String[] args`. Just return type, main, and parameters for command line arguments. `argc` gives you the number of command line
arguments passed through when running your program. For example: `$ ./test arg1 arg2` will have `argc`=3). `argv` is your list of arguments. `argc` will always be at
least 1. Why? The name of the program (in our case it's `test`) is considered an argument. Subsequently, it's also always the first argument in `argv`. Remember to
return something every time. This isn't a void function since the return value from main is used to determine error codes of your program on exit. Anything that isn't
zero is considered an error. It's also possible to leave the definition empty as `int main(void)` in
case your program does not use command line arguments.

## Including Other Packages

```c
#include <systempackage.h>
#include "some/path/to/your/package.h"

int main(...) {
    ...
}
```

Two ways of including packages. The first is any installed packages/libraries on the machine which are found in your "search path" (probably `/usr/include/`). This can be modified in more complicated projects with compiler options. 
The second way is to include any packages that aren't in your search path - you have to give the path to the header file (can be relative or the full path). 

> **Fun stuff:** These boxes will contain interesting information or more complicated examples 
throughout. Make sure to go through them when you feel like you got the basics down, or come
back to them later!

## `printf`

```c
#include <stdio.h>

int main(...){
    printf("<format>", arg1, arg2, ...);
    return 0;
}
```

Yep, you need to include a standard library package to use `printf()`. This should be simple enough - you provide a format and for every format identifier, you need to provide an
argument that corresponds to it (ie: `printf("%d\n", 5);` %d is the identifier for ints, 5 being arg1).

> **Fun stuff:** Man pages can be very helpful when looking for information about standard library functions. Try `man printf`!

## Types

Some standard primitives you'll be using in this class:

* `int` for integers, *usually* 32 bits. 
* `char` for characters, essentially an 8-bit integer
* `float` and `double` for floating points (*almost always* 32 and 64 bits)
* `long` for long integers, *usually* 64 bits. 
* `unsigned int` (can also make other integral types such as `long`, `char` etc. unsigned). Only `unsigned` means `unsigned int`.

Some types you might see popup from `<stdint.h>`:

* `int32_t` (signed 32-bit int)
* `uint32_t` (unsigned 32-bit int)

Why do we need these other types? The C standard does not specify exact byte counts for the data types. An integer on modern 64-bit computers is usually 4 bytes, but that's not the case for every
machine out there, especially if you consider mobile and embedded devices. 

## Anything With Conditionals

We'll be going through these quickly since these are straightforward

```c
// if-else statement
if (conditional) { ... }
else if (conditional) { ... }
else { ... }
```

```c
do { ... } 
while(conditional);

while(conditional) { ... }

// Old C standards such as ANSI/C89 need to have the variable declared beforehand.
int i; 
for(i = 0; i < x; i++) { ... }

// Newer standards allow declaration in the header like Java!
for(int i = 0; i < x; i++) { ... }
```

`conditional` here is a boolean (!) expression.

> **Fun stuff:** C does not have booleans, and conditionals are actually still interpreted as integers, with 0 being false and all other values being true. So, it's possible to check if `a` is not zero with just `if (a)`, although readability can suffer. Things like comparison operators evaluate to 0 when false and 1 when true. This means that weird things like `(x < y) + 3` are valid C, be careful!

## `switch`

C also has a switch statement just like Java, to be used with integers! Remember to `break` to
prevent cases falling through:

```c
int main(...) {
    int x = 1;
    switch (x) {
        case 1:    // both cases 1 and 2 reach the same code
        case 2:
            x = 3;
            break; // code continues to case 5 otherwise!
        case 5:
            x = 7;
            break;
        default:   // captures all other cases
            x = 16;
            break; // optional - for style
    }
    return 0;
}
```

## Some Useful Syntax Using Conditional Parameters

In C, assignments are also expressions evaluating to the assigned value, which makes some cool things possible. One thing you can do is initialize values inside a conditional parameter and use it as part of your conditional. This might save you some lines, especially in things like input reading loops. 

```c
int x;
if ((x = foo()) == 0) {
    ...
}

while((x = bar()) < MAX) {
    ...
}
```

> **Fun stuff:** Assignments being expressions means something like `if (x = 5)` is valid C, and 
will always be true because the expression evaluates to 5. Again, careful, this is an easy mistake to make when trying to write `if (x == 5)`! Most compilers will 
warn about this kind of usage though, which is nice. 

## Operators

Nothing out of the ordinary here. These are the same as Java. We'll get into bitwise operators later.

## Functions

We'll only be going over function headers here and save the rest for pointers


```c
int foo(int x, float y) {
    return x + x;
}

void bar() {
    return;
}
```

There's no public, private, protected, etc. since there are no objects and classes in C. Those concepts tie in with the OOP in Java. Just a return type, your function name, and parameters. Just as a word of
warning, sometimes the compiler let's you compile a program with functions that don't return anything despite having a return type. Using `-Wall` argument at compile
time(with `gcc` or `clang`) should stop this, but this isn't strictly required.

## Function Prototypes

USE THESE - not enough students use function protoypes - though for the most part the projects will force you to use them one way or another (because header files).

Function prototypes are useful since they let you globally reference functions, regardless of what order you write them in.

Why does this matter? Well, unlike Java, C doesn't let you write functions in any order and use them.

```c
int main(...) {

    foo(5, 'f'); 
    return 0;

    // The compiler will complain that foo() doesn't exist or there's an 
    // implicit declaration of a function.
}

void foo(int a, char b) { ... }
```

So how do we fix this? Write a function prototype before all of your functions. We'll get into how to clean this up later in header files for when you have too many function prototypes.

```c
void foo(int, char); // you don't have to put names for the parameters, just the types

int main(...) {
    ...
}

void foo(...) { ... }
```

You could also just write every function above the one you call it in, but function prototypes have other uses (again we'll expand on this in header files). They're also necessary in case you have mutually recursive functions that call each other. 

# The Essentials

The next few sections focus on the more important aspects of C that we'll be using a lot in this class (and in general if you're a C programmer).

## Structs

NOT objects. They aren't all that similar either. Structs don't belong to a class and they also don't hold any functions, which is key in objects. Structs are, however,
abstractions of a group of types.

Structs hold "members" or "fields" which are the data types that are valid to store in the struct itself.

```c
struct node {
    int data;
    struct node *next;
}; // don't forget this semi-colon!
```
The members of the struct (data and next) are the only valid data that you can put into this struct node. In memory, the size of a struct is at least the sum of sizes of its
members (can be larger due to *data alignment*, not discussed here).

## Struct initialization and referencing struct members (without pointers)


```c
int main(...) {
    struct node head = { .data = 4, .next = NULL }; // initializing like this might depend on the standard you're using

    // This is a more explicit way to declare and initialize
    struct node x;
    x.data = 5;
    x.next = NULL;

    // Come back to this after reading pointers, dynamic allocation and sizeof. 
    // Ignore for now!
    struct node *ptr = malloc(sizeof(struct node));
    ptr->data = 5;
    ptr->next = &head;
    int val = head.data; // referencing a member
    int ptr_next_val = ptr->next.data; // == val
    
    return 0;
}
```

It's important to note that structs that are declared but not initialized might not always be zeroed out. They can contain garbage values so you'll want to initialize
structs every time to ensure correctness - though this isn't completely necessary.

You can also make locally defined structs. Just do the same syntax inside a function. These would be unavailable for the world outside this function.

## Enums and Unions

This is condensed into one section since you won't be using these all that much, but you should know them anyway because they still have their uses outside of the
class.

### Enums

Enums are just ways to give names to numbers so that your program is easier to read and maintain. In general, giving a name to a value is useful, especially if you use
that value a lot for the same things (MACROS).

There's a few ways to declare these but here's the simplest one.

```c
// Classic example
enum months {
    JAN, //note this starts from 0, not 1 unless you specify as = 3 or something
    FEB,
    ... //etc.
};
```

### Unions

Unions are a special type of structure. The difference between a union and a struct is the fact that a union will only "contain" a single one of its members at a time. 
The size of a union is the size of its largest member. Unions are usually
used for statuses or flags or for interpreting different representations from the same underlying 
data. But really unions are generally good for space saving with anything that requires a large number of variables but only use one of them at the same time.

```c
union flags {
    int flag1;
    double flag2;
};
```

The size of the union in this case is 8 bytes (size of a double). If you populate flag1 with a value, you will only be using the lower 4 bytes of the union since an int
is only 4 bytes. Be careful, if you populate flag2 afterwards, you will lose the value of flag1. Similarly, if you populate flag2 first, and then populate flag1, you
will change only the lower 4 bytes, so if you access flag2 again, you'll get a different (or corrupt) value.

> **Fun stuff:** When using unions for a type that can contain multiple values, you may need to add
> another value to know which type is currently contained by the union. Here's a complicated example
> that also illustrates anonymous unions:
> ```c
> enum type { // For marking the contained type
>     INT,
>     FLOAT,
>     UNSIGNED
> };
> 
> struct mydata {
>     enum type type; // shows which type is contained right now
>     union { // no need to name it, self-contained!
>         int x;
>         float y;
>         unsigned u;
>     } data; // name of the union field, access int value as s.data.x
>     // NOTE: Can even avoid the name data after the C11 standard and use s.x, s.y etc.
> };
> 
> // Can use a switch to act based on type!
> int main(void) {
>     struct mydata s;
>     ... // fill it in somehow
>     switch (s.type) { // Enums are ints, so this works!
>         case INT:      printf("Has int: %d\n", s.data.x); break; 
>         case FLOAT:    printf("Has float: %f\n", s.data.y); break;
>         case UNSIGNED: printf("Has unsigned: %u\n", s.data.u); break;
>         default:       ... // an error case
>     }
>     return 0;
> }
> ```

## Pointers

A bit different from Java references. You can do a lot more with these. In other words, this is how you accidentally shoot yourself in the foot if you aren't careful.
Pointers are similar to references in the sense that... they point to things. They store the address of where a thing is. Maybe a better explanation would just be to show a practical example.


```c
int main(...){
int y = 5;
int *x = &y; // can also do int* x or int * x

int *a, b, c; //defines a(pointer) and ints b, c
int *d, *e, *f; //defines 3 pointers

*x = 6;

return 0;
}
```
`int* x` is declaring our pointer and we're assigning it to a reference of `y` - in other words, the address of `y`. When we want to change the value that `x` is pointing to
we dereference it using `*`. Changing the value that `x` is pointing to will also change the value of `y`. So in this case, we dereference `x`, which is pointing to `y`, and

change it to 6 -- `y` is now the value 6.

If we change the value of `x` itself, we run into problems that we may not want. Remember that C let's you do a lot of things, and this is one of them - changing what
the pointer is pointing to isn't necessarily wrong, you just have to know what you're changing it to. You could end up pointing to unwanted data and crash your
program, or worse, create security vulnerabilities. Since `x` is holding an address, changing that will make `x` point to another address. This could be useful for arrays,
which we'll get into later, but often you don't change the value of a pointer - you should only change the value that it's pointing to.

## Pass by Reference

C is a pass by value language, meaning anything you pass through a function will keep its value even if you change it in a function. This can be circumvented if you use pointers, but do note that
the pointers themselves are **still passed by value**. 
Literally passing by reference is not possible 
in C. (In
case you didn't know, Java is exactly the same with its references and passes them by value). 

```c
#include <stdio.h>

void foo(int a) {
    a = 6;
}

void bar(int* a) {
    *a = 6;
}

int main(...) {
    int a = 9;
    printf("%d\n", a);

    foo(a);
    printf("%d\n", a);
    
    bar(&a);
    printf("%d\n", a);

    return 0;
}
```

When run, the above code will produce the following output:
```
9
9
6
```

`a` in `foo()` won't change the value of `a` in `main()`. `bar()`, however, will since `bar()` takes in the address of `a` and dereferences it in order to change the value.
A function makes a copy of a variable when it's passed through, including pointers, 
so what you're really changing is that copy if you don't pass in a reference to it.

The same applies if you want to change the pointer itself (useful for arrays). You'll have to pass a pointer to the pointer.

```c
void foo(int **a, int **b) {
    *a = *b;
}

int main(...) {
    int z = 1;
    int *b = &z;
    int **a = &b;
    foo(a, &b);
    return 0;
}
```

This isn't the most practical example, but it should get the point across. Here we're changing what a (a double pointer) is pointing to, which is another pointer b. We
pass the address of b to foo() because of that copy pointer we mentioned earlier. If we only pass b (which is again a pointer), and set *a = b in foo(), we'll end
up pointing to the copy pointer, which won't exist after the function ends so we'll end up pointing to a garbage value later.

> **Fun stuff:** It is possible to have pointers to functions as well!
> Here's a basic example that's not very useful:
> ```c
> int add(int x, int y) { return x + y; }
> int sub(int x, int y) { return x - y; }
> 
> int main(void) {
>     // op is a pointer to a function taking two ints and returning an int
>     int (*op)(int, int) = add; 
>     int x = op(5, 3); // can be used as a function
>     // x is 8 here
>     op = sub;
>     x = op(5, 3);
>     // x is 2 now
>     return 0;
> }
> ```
> 
> This is very useful to obtain generic behavior in C. A good example is the standard library
> quicksorting function, `qsort` (see `man qsort`, as always):
> ```c
> void qsort(void *base, size_t nel, size_t width, 
>            int (*compar)(const void *, const void *));
> ```
> This function takes an arbitrary array `base`, and a comparison function `compar` for comparing
> them and does quicksort. `compar` takes two constant (explained later) void pointers, compares 
> them and returns an int. Unfortunately, it requires pointer tricks to use, and will be slower
> than one dedicated to a specific data type since a function is called for every comparison. 
> But it's widely applicable!


## Useful Practices with Pass by Reference

These techniques will be particularly useful in your projects when initializing values or changing values within functions all while avoiding overuse of pointers or when you want to obtain multiple return values from a function.

In general, it's good to avoid using pointers (and `malloc`) when not necessary - 
particularly with structs since it's more annoying to use '->' instead of '.'. 

For example, this can be useful for initialization:

```c
void init(struct node *x){
    x->data = 0;
    x->next = NULL;
}
```

Note that accessing members in a struct is different with pointers. 
Instead of using `.` you use `->`.
This is effectively a convenient shorthand, since `(*x).data` is much more cumbersome than
`x->data`. 

```c
int main(...){
    struct node x; // no pointers here, the struct is on the stack
    init(&x); // x is initialized after here
    return 0;
}
```

It is also possible to use a macro (see the Macros section) to make zero-initialization even simpler. A personal favorite!

```c
#define NODE_INITIALIZER { .data = 0, .next = NULL }

int main(...){
    struct node x = NODE_INITIALIZER; 
    return 0;
}
```

Multiple return values from a function (essentially modifying multiple external values via pointer):

```c
int foo(int *a, int *b, char *file_path1, char *file_path2) {
    *a = read(...);
    *b = read(...);
    return 0;
}
```

```c
int main(...) {
    int a;
    int b;
    int status = foo(&a, &b, "some/path", "another/path");
    return 0;
}
```

Why is this one in particular useful? In this case, we can now get how many bytes read from each file and get a status value from the function (if we had conditionals
checking for failure in the function, but in this case it always gives 0). Extremely useful when debugging a program and it also saves on extra function calls or
sometimes on lines of code. Get familiar with using these examples - they'll be useful in any C program you write.

## Malloc and Free

How to dynamically allocate memory for anything. C is not garbage collected like Java, so any
dynamically allocated memory also needs to be freed manually. Memory allocations are usually
done via `malloc()`, but there's also `calloc()` and `realloc()`. 

If you `malloc()` something, you `free()` it. Not doing so will cause 
memory leaks, meaning your program may consume more and more memory as time goes by.
This needs to be taken care of when programming in C and C++.

```c
#include <stdlib.h> // make sure to include this. malloc is in this header.
int main(...){
    int *a = malloc(4); // allocate space for an int; hopefully it's 4 bytes!
    free(a);
    return 0;
}
```
We'll get into complex uses of malloc() in arrays and some simplifications in the sizeof section. This section is going to show what exactly malloc is doing.

In this example we have a pointer a getting a dynamically allocated space of 4 bytes. `malloc()` takes in a value for how many bytes you want to allocate - and it
doesn't care what type it is. Remember that C is a language about memory and data management and every piece of data and memory is just a bunch of bits and
bytes to your computer (and to C somewhat). It doesn't care what the data is or what it looks like, it just needs to know what you want to do with it (we'll expand on
this in the casting section).

Since `malloc()` doesn't care about type, it returns a typeless `void *`. This type is automatically
casted to other pointer types in C, but an explicit cast like `(int *)` can also be added for style.

Now in this example, allocation isn't all that useful. We just got 4 bytes allocated to a pointer, which we could've done without using `malloc()`. When we get to arrays, we'll
see a much more meaningful example.

As mentioned earlier, when you `malloc()`, you `free()` it. Yes, the OS will recollect it all at the end of the program, but that doesn't mean you don't need to free
allocations. For example, when you reassign pointers that previously pointed to allocated space, you essentially lost that previously allocated space and now it's just
taking up valuable space with no more way to free the memory. Not great if you're working with large data sets that you constantly need to load in and out of memory. Not only that but you open your
program up to security vulnerabilities. If you're too lazy to `free()` see if there's a way for you to write certain parts of your code without `malloc()`.

An important difference between `malloc()` and static allocation is that dynamically allocated space lives on the heap while statically allocated data lives on the
stack. Knowing this distinction is important for many things (one of them being threading). Since
stack space is limited, refrain from doing large allocations (eg. arrays) on the stack, use `malloc` to
do large allocations. Remember your local variables/pointers still live on the stack though regardless of the allocation type.

A useful tool to check for memory leaks is `valgrind`. Just be careful, if you have too many stack frames (lots of recursion or forking or both), `valgrind` slows down
significantly.

## Arrays and Strings

We kept arrays for after pointers and `malloc()` because there are two ways to use arrays (well the second one isn't really an "array" but we can use it as one since
they're basically the same thing).

### Arrays

```c
#include <stdlib.h>

int main(int argc, char** argv) {
    int a[5] = {1, 2, 3, 4, 5}; // allocated on stack
    int *b = malloc(20); // allocated on heap
    for(int i = 4; i >= 0; i--) {
        b[i] = i; //initializing array values
        a[i] = i; // just doing something here to show array accesses are the same in both cases
    }
    return 0;
}
```

The first way to make an array is to statically allocate space and manually initialize it. This makes the size of the array immutable but if you know what values you
want (ie. a list of delimiters) and you don't need to change the size of the array.

The second way is through `malloc()` and a pointer. A pointer just points to the starting address of an allocation so that's what we'll use it for here. Notice the
number of bytes specified in `malloc()` - we requested 20 bytes since an integer is 4 bytes and we want 5 of them.

You most likely won't see the first usage too often particularly with integers - they aren't all that convenient either. It can be convenient with constant strings though!

Accessing and modifying an array is the same as Java regardless of which array type you use (static or dynamic).

### The Danger of C Arrays

C doesn't protect you against going over an array (`IndexOutOfBoundsException` in Java). You can set the `-fsanitize` and `-Werror` flags to check for heap buffer
overflows or stack smashing (read the article ["Smashing the Stack For Fun And Profit"](https://inst.eecs.berkeley.edu/~cs161/fa08/papers/stack_smashing.pdf) for why stack smashing and buffer overflows are crucial to know about). If you write past the end
of a buffer, C will let you do so. You can even read past the buffer if you want to in some cases.

```c
#include <stdio.h>

int main(...) {
    int a[5] = {1, 2, 3, 4, 5}; //valid indices 0-
    a[5] = 34;
    printf("%d\n", a[5]);
    return 0;
}
```

Here's an example of a) writing past your buffer and b) reading that written value. Both clearly index out of bounds, but C will let you do this (unless somehow a[5]
is beyond your process's stack space or heap space if you made a dynamic allocation - in both cases you'll segfault) - and GCC will let you compile unless it detects
stack smashing.

It is suggested that you go and try to segfault a simple program kind of like this one to find out what not to do. The best way to review is by doing - not reading... And
then forgetting.

> **Fun stuff:** `[]` is essentially synctactic sugar for pointer arithmetic in C (come back after the pointer arithmetic section!). `a[4]` is exactly the same as `*(a + 4)`. As such, it is possible to write weird things like `a[-10]` or `4[a]`. 

## Strings

C doesn't have a formal way to make or use strings. Instead they're always char arrays with a special ending to the buffer - the null terminator ('\0', a zero byte). The null
terminator lets the program know where the end of the `char` buffer is (we'll call it string from now on). Without it, if you try to print out a string, the program won't
know where to end the buffer and will keep going until it finds a null terminator. Similar to what we mentioned before, you can read past the end of an array so C
strings will keep doing that unless a null terminator is specified. What this means is that your printed string might contain garbage values since it doesn't know how
to interpret those values.

```c
#include <stdlib.h>
#include <stdio.h>

int main(...) {
    char a[6] = "hello";
    char *a2 = "hello"; // similar to the above but getting the size of this will be more difficult (read sizeof section), and the location in memory is different (read-only)
    char *b = malloc(6);
    snprintf(b, 6, "hello"); // don't worry about this. it's just filling in the array - the function also null terminates conveniently.
    return 0;
}
```

String a is a static, immutable string and it is null terminated for you. Since "hello" is a five letter word, we need 6 spaces for the null terminator at the end.

In the case you `malloc()` a string, you will need to null terminate it yourself if you manually fill it in. In this example, `snprint()` is used for convenience to fill in the
buffer and null terminate it.

A word of warning - not all functions null terminate, particularly the ones from `<string.h>` (ironically) - so be wary of that.

> **Fun stuff:** In Microsoft's C library, _snprintf was historically the only available version of the function which did not null terminate. Go Linux.

If you need a list of the buffer functions in `<stdio.h>` or string functions in `<string.h>`, 
[cppreference](https://en.cppreference.com/w/c/header) is a nice resource. 

## `sizeof`

Another topic we left out until now since we wanted to group all of its examples in one spot instead of spread out in different sections.

`sizeof` is an extremely convenient operator that can be used for a multitude of things, but its most common usage is in `malloc()`. In our earlier examples of
`malloc()` we were explicitly putting in the number of bytes we wanted to allocated by doing some simple math. But this is inconvenient and annoying if you need to
remember what size a certain type is for every machine (again, this isn't all that relevant anymore but isn't something we should ignore either).

Here are some of the different usages of `sizeof`:

```c
#include <stdlib.h>
#include <stdio.h>
int main(...){
    printf("%d\n", sizeof(int)); // prints 4
    printf("%d\n", sizeof("hello")); // prints 6

    double *x = malloc(sizeof(double) * 4); //allocated 8 * 4 bytes

    int a[5] = [1, 2, 3, 4, 5];
    printf("%d\n", sizeof(a) / sizeof(int)); // size of a static array, prints 5

    double *b = malloc(sizeof(*b) * 4); // convenient alternative, still works if type of b changes
    printf("%d\n", sizeof(b)); // only prints 8 since b is a pointer, no static array information 

    return 0;
}
```
Note in the last example, we get the size of a static array. This way of getting the size doesn't work for dynamically allocated arrays since the `sizeof(a)`, if `a` were a
pointer declared as `int *a`, would be 8 bytes, as with `b`. In this example though, `sizeof(a)` gives 20, which is then divided by the size of an `int` to get the number of elements.
Passing an array to a function always decays it to a pointer and `sizeof` will show 8 bytes, because
the static size information is not retained.

## Casting

Casting in C works differently from Java. In Java, if you cast data to a certain type, it will try to shape it into that type - hence why it sometimes (all the time)
complains that you can't cast a certain type into another.

But remember that C doesn't care. Data is just a bunch of 1's and 0's - it doesn't matter what the shape is. You can turn anything into anything. Literally. When casting pointers of course! Casting
integral types to floating point and vice-versa will still perform the proper conversion. 

You have a struct that's an abstraction of a node? Cast it to a `char *` because why not. It'll probably come out with garbage values when you try to print it but you can
do it.

```c
int main(...) {
    unsigned long x = 0xFFFFFF;
    void *ptr = (void *) x; // you may need to do things like this!
}
```

Another example is when you're reading data from a buffer (for example reading a buffer from a socket).

```c
int socket = some_file_descriptor
int number;
write(socket, (char *) &number, sizeof(int)); // address of "number" then casted to char*
```
The cast is a little unnecessary here since the int will automatically get casted to char* when it's passed through the function, but again good practice and also to
stop the compiler from complaining with a bunch of warnings.

One more example just to show you that you can really do whatever you want, though not without consequences (so many compiler warnings!)

```c
#include <stdio.h>
int main(...) {
    int a = (int) "abcd";
    printf("%s\n", (char *) &a); //,this will print garbage values and not "abcd" but the point is that C will let you run this.
    return 0;
}
```

## Bit Manipulation

Get used to bit manipulation in Operating Systems. They're everywhere. Bit shifting, masking, toggling, etc. 

If you're wondering why it's so important - bit shifting is fast... really fast (just a single instruction fast). Not only that it allows you to save space keeping track of
things. For example, to keep track of allocated blocks in disk (let's say it has 32 blocks), you can use a single int to keep track of the block status. The first bit for the
first block, second for the second, etc.

Here's a quick rundown:

```c
int main(...){
    int x = 1;
    x = x << 1; // left shift by one. x is now 2. You can shorten this by doing x <<= 1
    x >>= 1; // right shift by one. x is now 1. This one is already shortened syntax
    if(x & 1) { ... } // bitwise AND
    if(x | 1) { ... } // bitwise OR
    if(~x) { ... } // bitwise NOT
    if(x ^ 1) { ... } // bitwise XOR
    return 0;
}
```

> **Fun stuff:** C does not have a logical shift operator (`>>>`) like Java, but this is less necessary since you usually just use `unsigned` values for bit fun.

## Bit Masking

This is a bit tricky at first but once you practice it enough you'll understand it. What you're doing here is extracting bits out of a value to get only certain bits that you
want. Again, this is commonly used when tracking the status of things in your operating system so you'll need to get used to seeing these.

Using unsigned integers here makes things easier, as otherwise we don't always get the desired 
result for some of these. Shifting negative numbers can be confusing because the shifts are
arithmetic. 

If we had the hex 0xFF (one byte of all 1's) and we wanted only the least significant 4 bits, we would do:

```c
uint32_t last_four = 0xFF & 0xF;
```

0xF is our mask, and this is just 4 bits of 1's. If we bitwise AND, then it'll give us what the last four bits of 0xFF are. If we convert it to binary it looks like this

```
0xFF: 1111 1111
0x0F: 0000 1111
---------------
0000 1111
```

If we wanted the most significant four bits, we would have to right shift 0xFF by 4 bits and then apply the mask with bitwise AND again.


```c
uint32_t first_four = (0xFF >> 4) & 0xF;
```

A more practical example: (here's a tip in this problem as well. If you're converting from decimal to binary, sometimes it's easier to convert to hex and then write out
the binary representation of each hex digit, and vice versa)

```c
uint32_t mask = 33; //   00100001 in binary or 0x21 in hex
uint32_t value = 104; // 01101000 in binary or 0x68 in hex
```
```c
uint32_t AND_mask = value & mask; // 00100000 or 32 in decimal or 0x20 in hex
uint32_t OR_mask = value | mask; //  01101001 or 105 or 0x69
```

Here's how you toggle a bit:

```c
uint32_t x = 0b001100101; // binary in C is a compiler extension, but exists in C++14 and beyond
uint32_t toggle = 1;

// Let's toggle the 5th bit
x ^= (toggle << 4); // now its 01110101
x ^= (toggle << 4); // back to 01100101
```
# Not Essentials But Must Haves

The topics below are really not essential to C programming but are heavily used in and out of this class. If you want to be a good C programmer, these are must
haves.

## Typedef

A handy concept for shortening down type definitions. Notice how you need to type out struct node every time you declare a variable. This get's really annoying
after a while especially if you malloc structs constantly or write a lot of functions using structs. But it really helps in code readability.

> **Fun stuff:** [Linus Torvalds begs to differ](https://www.kernel.org/doc/html/v4.14/process/coding-style.html#typedefs)... Pepper ain't he? TL;DR - don't hide things unnecessarily behind typedefs. Particularly pointers.

```c
// you can typedef immediately in a struct definition
typedef struct node {
    int data;
    struct node next;
} Node; 

// same as struct node
void init(Node *node) { ... }
```

```c
// alternatively
typedef struct node Node;

// you can also typedef primitives, and also more complicated things like function pointers
typedef unsigned int my_uint32;
```
## Macros

One of the best things about C. You can do so many things with macros, including generic containers
which are pretty annoying to code. 

The first thing about macros - they are precompiled meaning you compiler itself doesn't check their syntax. This is because macros are
essentially just find and replace definitions. Any instance of a macro name will be replaced with the macro definition so you should be careful in naming your macros or using
common names as variables. In general a good practice is to capitalize all of the letters in macros.

The second thing about macros - they're fast, but only if you use them correctly. Macros are a decent way to avoid function calls which have the disadvantage of
creating stack frames if they're not inlined. If you have a function that's only one line or has small functionality (for example bit shifting/masking is really fast and doesn't require that much code) you might want to use a macro instead. One advantage of macros is that they can be type agnostic.
For example, consider the following macro that finds the maximum of two expressions (remember
the ternary conditional expressions from Java, which are the same in C):

```c
#define MAX(x, y) ((x) > (y) ? (x) : (y))
```

> **Fun stuff:** The excessive use of parentheses prevents precedence mix-ups that can happen due to text replacement. What happens if you pass `a >> 2` as an argument and there are no parentheses?

Compared to a function, this will work with all kinds of expressions - signed, unsigned, 
long, short, double... The disadvantage is that expensive expressions such as function calls will
be repeated since this is text replacement:

```c
MAX(f(), g() + 3)
// substitutes into
((f()) > (g() + 3) ? (f()) : (g()))
```

This would not happen with a function, since the arguments would be evaluated before being passed.
Some weird behavior will also pop-up here if your function has side effects, 
[so better be careful
with macros](https://dustri.org/b/min-and-max-macro-considered-harmful.html)!

Macros aren't for everything too. If your macro is just calling a function, you lose the benefits of using a macro.

Third thing about about macros - and it's bad - you can't debug them. Yep, if you use a debugger like GDB and run into a macro, GDB will just skip over the code in the macro. This is
what makes long macros super annoying - but you can extract a lot of utility from them too. 

General good practice with macros - K.I.S.S. (keep it stupid simple). Just a good thing to remember in operating systems sometimes.


```c
// simple macros
#define N 15
#define KiB 1024 // kibibyte
#define MiB KiB*1024 // Mibibyte
```
```c
// functional macros, like max (use parentheses well in real code!)
#define SUM(A, B) A+B
#define TOGGLE(X, SHIFT) X ^= 0x1 << SHIFT
```
```c
// multiline functional macros
#define DEBUG(X, ERR)       \
    do {                    \
        if(ERR) {           \
            printf(X, ERR); \
        } else {            \
            printf("PASS"); \
        }                   \
    } while(0)
```

The multiline macro might need some explaining. First, you don't need to line up the \, it just looks neater. Second, the do-while (0) is generally good to include in
every macro. There's [a stackoverflow answer](https://stackoverflow.com/questions/1067226/c-multi-line-macro-do-while0-vs-scope-block) on why but in short, it's so that the macro expands properly in code blocks (conditionals, loops, etc.) with or without
brackets surrounding the block, and without leaking local variables into the enclosing block. 
Notice that it needs a semi-colon at the end to be complete. 

You should always surround your macro definitions with conditional checks. If you don't you will override any existing macros with the same name:

```c
#ifndef
#define MACRO1 something
#define MACRO2 something
...
#endif
```

In your makefile you can also define which macro definitions to push to your code. This is useful for debugging - sometimes you don't want debug messages to show
up and other times you do. You'll see an example of this in some of the project makefiles where we push certain macros to enable/disable certain blocks of code. Something like:

```c
#ifdef DEBUG
// some code
...
#endif
```

The code above will only be compiled when the `DEBUG` flag is defined, which can be done via compiler arguments too. 

> **Fun stuff:** With great power, comes great shooting-yourself-in-the-foot risk. [This is the case for macros](https://edryd.org/posts/macro-dangers/), just as it was with pointers. Summarizes C pretty well. 

## Header Files

The all important header file. This can get its own document with how much you can do with it, but we'll simplify it here.

First, if you have any functions that you want to make globally available to any source file that includes your header file, put your function prototypes in that header file
- you don't have to put the prototypes in the .c file. The same goes with macros, structs, typedefs, or any other packages you want to include.

It's best to keep common packages out of the header file. The precompiler just takes you header file and basically copy pastes it to the top of your source file (not
exactly but close enough for what we need it for) so any packages included in your header, will be included in every file that includes your header file. Try to be wary of
this so you don't repeat common definitions, includes, or end up with circular dependencies.

Second, always surround the header with conditional checks. Having multiple conditional checks might be better but you won't have to worry about that in this class.
You'll see them in the example.

Header File (`mylib.h`)

```c
//ifndef -> if not defined, ifdef -> if defined
#ifndef TEST_H // this is just checking on a variable name TEST_H. Is used to avoid getting included again.
#define TEST_H // define here, so the header does not get expanded again if re-included

/* include any necessary packages for your .c file that other people might also
want to use (but not explicitly include). Generally speaking, you don't need to
include the packages that are in this example in the header file - let the user
decide that - but you might want to include header files that might contain
some definitions needed to call the functions in this header file (macros or
typedefs from other header files for example). This decision is a little bit of
a headache and a lot of the time you'll see common packages included in header
files anyway. */
#include <pthread.h>

#define MACRO ...
...

typedef struct X{
    int member1;
    char* member2;
} Y;

// Parameter names are not necessary, but can be helpful as documentation
void function1(int, int);
int function2(char *string, void *buffer);

#endif // <- This ends the #ifndef block at the start
```

> **Fun stuff:** Since naming header guards can get cumbersome, many compilers support putting
the directive `#pragma once` at the top of the file instead, which does the same. This is 
not a standard C/C++ feature though! Good to know if you see it in the wild.

Your C library file (`mylib.c`)

```c
#include <stdio.h>
#include <stdlib.h>
#include "path/to/mylib.h" // make sure to include the header in your library file

// Define the prototyped functions

void function1(int a, int b){ // do something

}

int function2(char *a, void *b){ // return something

}

```
The C file using your library file (`main.c`)

```c
#include <stdio.h>
#include <stdlib.h>
#include "path/to/mylib.h"

int main(...){
    int x = MACRO1;
    Y *z = malloc(sizeof(Y)); // poor naming choice, I know
    function1(1, 2);
    return 0;
}
```

When compiling `main.c`, you'll need to pass both files in this case:
`$ gcc mylib.c main.c -o main`.

> **Fun stuff:** The standard library includes are also just header files containing prototypes and macro/struct definitions, but not the function code. The compiler takes care of linking the actual code for them automatically, but it's necessary to provide extra linker flags when using third-party libraries or even the math library (with the `-lm` flag.)

# Other Important Things

Things that are useful, but won't be explained too in-depth. These are still important but either they're used less often or you'll be learning more about them during the course. It's up to you guys to figure out how to use these (man pages, online documentation, etc.).

## Pointer Arithmetic

Pointer arithmetic isn't something you'll be using super often, but can be pretty important.

Pointer arithmetic is simple. You take a pointer... and you add to it.

What use does this have? Didn't we establish earlier that we don't want to stray from the address a pointer is referring to? The answer is (mainly) arrays, but also some other low-level stuff.


```c
#include <stdio.h>

int main(...){
    int a[5] = {1, 2, 3, 4, 5};
    print("%d\n", *(a + 3));
    return 0;
}
```

This will print 4 since a is pointing to the start of the array and we accessed an offset of 3. Something to remember is that when doing pointer arithmetic, the
number of bytes you jump is based on type. So when you do `a+3`, you're really doing `address_at_0 + 3*sizeof(int)` bytes, the number of bytes jumped depends on the pointer type.

It's also possible to subtract two pointers of the same type from each other and get an offset. 
Don't think about trying multiplication and division though...

## `open()`, `read()`, `write()` etc.

We'll go over these during class, since these functions from `<unistd.h>` are all about interfacing with the operating system.

> **Fun stuff:** Not to be confused with the f-variants, `fopen()`, `fread()` etc. which are standard library 
> functions used for most common file I/O in C. Feel free to learn about those on the internet. 
> Here's a small file-reading example with them which doesn't do any error checking, just to give
> you a very basic idea.
> ```c
> #include <stdio.h> // also contains file I/O stuff
> 
> int main(void) {
>     FILE *file = fopen("path/to/file.txt", "r"); // read mode
>     char buffer[64]; // array to read data into
>     fread(buffer, 1, 64, file); // read 63 items of size 1 from the file
>     fclose(file); // close the file
>     return 0;
> }
> ```
> These standard library functions will use `read()`, `write()` etc. in the background! Or different
> functions if you're compiling on Windows. Unifying these in a system-independent manner is why
> standard libraries are so convenient!

## `memcpy()`, `memset()`, `calloc()`

Rather than initializing every member of a struct or array manually, if you just need everything to be NULL or zeroed out, you can just use `memset()`. Alternatively, you can use
`calloc()` which is similar to using `malloc()` but returns zeroed memory.

## `const`

Similar to `final` in Java, but way better, e.g. `const int x;`. For simple variables, it prevents their modification. For pointers, things get a bit confusing. [It is possible to prevent the value *pointed to* from being modified, as well as the pointer itself](https://stackoverflow.com/questions/1143262/what-is-the-difference-between-const-int-const-int-const-and-int-const). Very useful when defining read-only functions to make sure
they don't modify anything in passed arrays/structs. 

> **Fun stuff:** Hope you enjoyed this 'book' and got up to speed a bit. There's a lot more, so feel free to read around, code up some examples, and remember you can ask your TAs anything! 

#### Adapted from work by Daniel Kim, by the CS-323 staff.


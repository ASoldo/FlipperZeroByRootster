[Inspiration Video](https://www.youtube.com/watch?v=FBs7UoPy-sc&list=PLlrATfBNZ98fqE45g3jZA_hLGUrD4bo6_&index=3) from the past! @TheCherno

Here is an example of C code that declares and initializes variables of all the primitive data types:

```c
#include <stdio.h>
int main(void) {    /* void as param means this function takes no params*/
	char c = 'A';      /* character */     
	char st[] = "String";     /* string */
	short s = -32768;   /* short integer */     
	int i = -2147483648; /* integer */     
	long l = -9223372036854775807; /* long integer */     
	long long ll = -9223372036854775807; /* long long integer */     
	float f = 3.14f;   /* single-precision floating-point */     
	double d = 3.14;   /* double-precision floating-point */     
	long double ld = 3.14L; /* extended-precision floating-point */     
	_Bool b = 0;       /* boolean */     
	printf("char : %c\n string : %c\n short : %d\n int : %d\n long : %ld\n long long : %lld\n float : %f\n double : %lf\n long double : %Lf\n boolean : %d\n", c, s, i, l, ll, f, d, ld, b);     
	return 0; 
}
```

```c
gcc -c -fsyntax-only main.cc
```
>  can be used to check for errors in specific file without creating the output.

In this example, we have a `main` function that declares and initializes a variable of each of the C programming language's primitive data types:

-   `char`: a single byte that can store a character or small integer and is represented with single quotes ''.
-   `char st[]`: can store more than 1 character and is represented with double quotes "". 
-   `short`: short integer.
-   `int`: integer
-   `long`: long integer
-   `long long`: long long integer
-   `float`: single-precision floating-point
-   `double`: double-precision floating-point
-   `long double`: extended-precision floating-point
-   `_Bool`: a boolean type, can store either `0` or `1`.

The program then uses the `printf()` function to print the values of each variable.

The `printf()` function is declared in the `<stdio.h>` header file, which is a standard C library header that provides input and output functionality and it is implemented by the C runtime library which is a part of the operating system.. To use the `printf()` function in a C program, you have to include the `<stdio.h>` header file at the top of the source code file.

It is worth noting that the size of the data types might be different depending on the system and architecture. Also, the initialization value of long long and long int should be written with suffix `LL` and `L` respectively.


```c
#include <stdio.h>  
int main()  
{  
	printf("hello, world!\n");
	return 0;
}
```

# 1. Preprocessing

During compilation of a C program the compilation is started off with preprocessing the directives (e.g., #include and #define). The preprocessor (_cpp_ — c preprocessor) is a separate program in reality, but it is invoked automatically by the compiler. For example, the `#include <stdio.h>` command in line 1 of `main.c` tells the preprocessor to read the contents of the system header file `stdio.h` and insert it directly into the program text. The result is another file typically with the `.i` suffix. In practice, the preprocessed file is not saved to disk unless the `-save-temps` option is used.

This is the first stage of compilation process where preprocessor directives (macros and header files are most common) are expanded. To perform this step gcc executes the following command internally.

```c
cpp main.c > main.i
```

The result is a file `main.i` that contains the source code with all macros expanded. If you execute the above command in isolation then the file `main.i` will be saved to disk and you can see its content by vi or any other editor you have on your Linux box.

# 2. Compilation

In this phase compilation proper takes place. The compiler (_ccl_) translates `main.i` into `main.s`. File `main.s` contains assembly code. You can explicitly tell _gcc_ to translate `main.i` to `main.s` by executing the following command.

```c
gcc -S main.i
```

The command line option -S tells the compiler to convert the preprocessed code to assembly language without creating an object file. After having created `main.s` you can see the content of this file. While looking at assembly code you may note that the assembly code contains a call to the external function `printf`.

# 3. Assembly

Here, the assembler `as` translates `main.s` into machine language instructions, and generates an object file `main.o`. You can invoke the assembler at your own by executing the following command.

```c
as main.s -o main.o
```

The above command will generate `main.o` as it is specified with -o option. And, the resulting file contains the machine instructions for the classic "Hello World!" program, with an undefined reference to `printf`.

We can inspect main.o with the following command.
```c
objdump -t main.o
```

# 4. Linking

Step 4/4) Link `main.o` with other necessary object files namely, crti.o & crtn.o (they define function prologs & epilogs, respectively), crt1.o (contains _start symbol for bootstrapping the initial execution of the program), libc.so path or -lc flag for libc and then finally set the name of the dynamic linker, to generate a dynamically linked ELF executable:

On x86_64:

```c
ld /usr/lib/x86_64-linux-gnu/crti.o /usr/lib/x86_64-linux-gnu/crtn.o /usr/lib/x86_64-linux-gnu/crt1.o -lc main.o -dynamic-linker /lib64/ld-linux-x86-64.so.2 -o main.sh
```

OR (if you'd like to specify path to libc.so)

```c
ld /usr/lib/x86_64-linux-gnu/crti.o /usr/lib/x86_64-linux-gnu/crtn.o /usr/lib/x86_64-linux-gnu/crt1.o /usr/lib/x86_64-linux-gnu/libc.so main.o -dynamic-linker /lib64/ld-linux-x86-64.so.2 -o main.sh
```

On 32-bit ARM:

```c
ld /usr/lib/arm-linux-gnueabihf/crti.o /usr/lib/arm-linux-gnueabihf/crtn.o /usr/lib/arm-linux-gnueabihf/crt1.o -lc main.o -dynamic-linker /lib/ld-linux.so.3 -o main.sh
```

OR (if you'd like to specify path to libc.so)

```c
ld /usr/lib/arm-linux-gnueabihf/crti.o /usr/lib/arm-linux-gnueabihf/crtn.o /usr/lib/arm-linux-gnueabihf/crt1.o /usr/lib/arm-linux-gnueabihf/libc.so main.o -dynamic-linker /lib/ld-linux-armhf.so.3 -o main.sh
```

The `ld` command is used to link object files together to create an executable program.

-   `ld` is the command to invoke the linker
-   `/usr/lib/x86_64-linux-gnu/crti.o`, `/usr/lib/x86_64-linux-gnu/crtn.o`, and `/usr/lib/x86_64-linux-gnu/crt1.o` are object files being linked
-   `-lc` tells the linker to link against the standard C library
-   `main.o` is an object file containing the `main` function of the program
-   `-dynamic-linker /lib64/ld-linux-x86-64.so.2` specifies the dynamic linker to be used
-   `-o main.sh` specifies the output file to be named `main.sh`.

The final executable output file is named `main.sh`. It is linking object files like crt1.o, crti.o, crtn.o and main.o to create an executable named main.sh and linking it with standard c library and dynamic linker is /lib64/ld-linux-x86-64.so.2

### You can then run the ELF executable 'main.sh':

>! If you have permission issues you can try

```sh
chmod u+x ./main.sh
```

Run Application.

```c
./main.sh
```
> Hello World !

You can now try the following but delete all files except `main.c`.

```c
gcc -save-temps *.c
```

#### Sources:

> [https://linux.die.net/man/1/gcc](https://linux.die.net/man/1/gcc)
> 
> [https://linux.die.net/man/1/ld](https://linux.die.net/man/1/ld)
> 
> [https://dev.gentoo.org/~vapier/crt.txt](https://dev.gentoo.org/~vapier/crt.txt)

## Extra:

#### Dynamic Linker
>`/lib64/ld-linux-x86-64.so.2` is a dynamic linker and loader, it is a program that loads and links shared libraries at runtime. It is used by the operating system when starting a dynamically linked program.
>When a dynamically linked program is executed, the operating system starts the program by loading the dynamic linker, which in turn loads the shared libraries that the program depends on. The dynamic linker then resolves the symbol references between the program and the libraries, and transfers control to the program's entry point.
>This file, in specific, is the dynamic linker for 64-bit x86 architecture on Linux systems. It is commonly located in the `/lib64` or `/usr/lib64` directory.
>This linker is particularly useful for programs that use shared libraries, as it allows the program to use the same library code for multiple programs, reducing the amount of memory and disk space used. It also allows for library updates without the need to recompile or relink all programs that use the library.
>It is also useful for running multiple version of the same library and allows to have multiple version of library coexist peacefully on same system.

#### Object files
>The files `/usr/lib/x86_64-linux-gnu/crti.o` , `/usr/lib/x86_64-linux-gnu/crtn.o` and `/usr/lib/x86_64-linux-gnu/crt1.o` are object files that are used in the process of creating an executable program.
>`crt1.o`, `crti.o`, and `crtn.o` are part of the C runtime library, which provides functions and data structures that are used by the system to start and exit a C program.
>`crt1.o` contains the `_start` function, which is the entry point for the program and performs basic initialization tasks such as setting up the stack and calling the `main` function.
>`crti.o` contains the code for the constructor functions (functions called before .main()) and `crtn.o` contains the code for the destructor functions (functions called after main()).
>`crti.o` and `crtn.o` are used to implement the global constructors and destructors, which are functions that are automatically called before and after `main()`. These functions are used to initialize and cleanup global variables and to perform other tasks that need to be done before and after the program runs.
>All these files are located in the `/usr/lib/x86_64-linux-gnu/`directory and they are specific to the x86-64 architecture and the linux operating system.
>These files are used to provide a standard way to perform the setup and cleanup tasks that are necessary for a C program to run. They are also used to ensure that the program is initialized and terminated in a consistent and predictable way, regardless of the specifics of the system on which the program is run.
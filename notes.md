## What Is Format String?

Format string is a specifier which tells the program the format of the output.

| Format String/Specifier | Description  |
| ------------- |:-------------:| 
| %c | Character | 
| %s | String | 
| %d | Integer | 
| %n | Number of bytes written so far | 
| %x or %X | Output hexadecimal number | 

Example : 

```c
# touch example.c
# nano example.c
# cat example.c
#include <stdio.h>
int main()
{
    char a[] = "example";
    printf("%s\n", a);
    return 0;
}
# gcc example.c -o example
# ./example
example
```

## What Is a Format String Bug (FSB)?

Format string bug occurs when user input data include format specification string. The attacker input a number of format specifiers that have no arguments on the stack, and values from the stack are used in their place. This leads to information disclosure and execution of arbitrary code.

Format string bug exists in most of the printf family :

```
printf
fprintf
sprintf
snprintf
vfprintf
vprintf
vsprintf
vsnprintf
wprintf
```

& any similar functions that accept a string that can contain C-style format specifiers.

I found a really good example in shellcoders handbook, a program that calls `printf` with an argument :

```c
# touch exp.c
# nano exp.c
# cat exp.c
#include <stdio.h>
#include <stdlib.h>

int main( int argc, char *argv[] )
{
        if( argc != 2 )
        {
                printf("Error - supply a format string please\n");
                return 1;
        }
        printf( argv[1] );
        printf( "\n" );

        return 0;
}
# gcc exp.c -o exp
# file exp
exp: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.18, not stripped
```

If we run the binary now like this : 

`# ./exp "%x %x %x %x"`

We are calling printf like this : 

```c
printf( "%x %x %x %x" );
```

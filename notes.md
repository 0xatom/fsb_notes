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

& any similar functions that can contain C-style format specifiers.

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

The important thing here is that we haven’t specify the four numeric variables into the string, printf() doesn’t fail instead  producing output that looks like this :

```
# ./exp "%x %x %x %x"
b7ff1040 804849b b7fd7ff4 8048490
```

So printf() takes four arguments from somewhere. These arguments are coming from the stack.

This may appear not to be a problem, however an attacker might be able to see the contents of the stack. What does that mean? It might reveal sensitive information such as usernames & passwords.

Now if we try to input a large number of `%x` format specifiers & "A" we will get this :

```
# ./exp "AAAAAAAAAAAAAAAAAAA%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x%x"

AAAAAAAAAAAAAAAAAAAb7ff1040804849bb7fd7ff480484900bffffc68b7eadc762bffffc94bffffca0b7fe1848bffffc50ffffffffb7ffeff480482691bffffc50b7ff0626b7fffab0b7fe1b28b7fd7ff400bffffc68fc1008a4d65f9eb4000280483800b7ff6210b7eadb9bb7ffeff428048380080483a180484342bffffc9480484908048480b7ff1040bffffc8cb7fff8f82bffffdadbffffdb30bffffec9bffffed3bffffef3bfffff07bfffff0fbfffff1bbfffff26bfffff39bfffff46bfffff4ebfffff59bfffff9bbfffffacbfffffbcbfffffc5020b7fe241421b7fe200010f8bfbff61000116438048034420577b7fe30008098048380b0c0d0e017019bffffd8b1fbffffff6fbffffd9b000073000000f44ba45051ed81f361b5575769b78269363836000652f2e0041007078414141414141414141414141414141417825414178257825782578257825782578257825
```

We are taking a large amount of data from the stack, but in the end of the string we see the hex of our string :

```python
>>> "41414141414141414141414141414141".decode("hex")
'AAAAAAAAAAAAAAAA'
```

Some info that book clears up :

```
1) They don’t affect only *nix systems.
2) They aren’t necessarily stack based.
3) Stack protection mechanisms will not generally defend against them.
```

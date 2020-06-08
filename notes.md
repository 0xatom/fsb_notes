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



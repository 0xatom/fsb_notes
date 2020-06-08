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


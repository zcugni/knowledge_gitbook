# C

## Disclaimer

I started writing the notes way after i learned C, and i didn't have the courage to fill this page. Also, i kind of forget stuff since then. I just add stuff on this page as I stumble again on it, but it's by no mean complete.

{% hint style="info" %}
To do : Add all the syntax exemples
{% endhint %}

## Include

* Files can be included with : 
  * `#include <lib_filename>` Will search for it in standard include path
  * `#include 'local_filename'` Will search for it in the current directory
* Those are headers file \(`.h`\)that contains functions prototype

## Struct

* A struct is a variable that groups multiple variables
* An example is the `tm` struct for time :

```c
struct tm {
    int tm_sec;
    int tm_min;
    int tm_hour;
    int tm_mday; // day of the month
    int tm_mon; // month
    int tm_year;
    int tm_wday; // day of the week
    int tm_yday; // day in the year
    int tm_isdst; // daylight saving time
};
```

* An element of a struct can be access with a `.` : `<struct_name>.<element_name>`
* If you have a pointer to a struct, you can access it's element with a `->` : `<ptr_name>-><element_name>`

## Pointers

{% hint style="info" %}
Fill with syntax
{% endhint %}

* You can also create a pointer to a function : `<return_type> (*<ptr_name>)(<param_type>, ...) = &<func>`
  * Example : `int (*func_ptr)(int, int) = &add;`
  * Use it like this : `(*fun_ptr)(10, 13);`

## Main function

* The main function can be declared in 2 ways :
  * `int main(void)`
  * `int main(int arg_count, char *arg_list[])`
    * `arg_count` Contains the number of arguments
    * `arg_list` List of the arguments  as strings \(also called an **argument vector**\)
    * The 0th argument is the executable name

## Useful functions

### Printf

* Base format string function in C
* Common format specifiers :
  * `%s` Strings
  * `%d` Decimal number
  * `%x` Hexadecimal number
  * `%p` Memory address \(equivalent to `0x%08x`\)
  * `%n` Will write the number of character written so far at this address
* Define the minimum width of an element with `%<nb><format_specifier>`
  * It will pad the element to nb length with space
  * To pad it with 0, use `%0<nb><format_specifier>`
  * Example : `%8d`
  * This is called a **field width parameter**
* **Direct parameter access** let you access any of the printf arguments
  * Syntax : `%<nb_arg>$<specifier>`
  * Example : `printf("This is the 7th arg : %7$d", 1, 2, 3, 4, 5, 6, 7, 8, 9")`
  * Combination with field with parameters :  `%<nb_args>$[0]<min_width><format_specifier>`
    * Example : `%4$05d`

{% hint style="info" %}
Check [this website](https://www.lix.polytechnique.fr/~liberti/public/computing/prog/c/C/FUNCTIONS/format.html#width) for a more complete breakdown of options
{% endhint %}

### Open

* Open a file
* Access flags :
  * * `O_RDONLY` Open file for read-only access 
    * `O_WRONLY` Open file for write-only access
    * `O_RDWR` Open file for both read and write access
* Other common flags :
  * `O_APPEND` Append to the end of the file if it already exist
  * `O_TRUNC` Truncate the file to a length of 0 if it already exist \(aka, replace its content\)
  * `O_CREAT` Create the file if it doesn’t exist, additional arguments are needed for permissions :
    * `S_IRUSR` Gives the owner read permission
    * `S_IWUSR` Gives the owner write permission
    * `S_IXUSR` Gives the owner execute permission
    * `S_IRGRP` Gives the group read permission
    * `S_IWGRP` Gives the group write permission
    * `S_IXGRP` Gives the group execute permission
    * `S_IROTH` Gives everyone else read permission
    * `S_IWOTH` Gives everyone else write permission
    * `S_IXOTH` Gives everyone else execute permission
* These flags can be combined with the bitwise OR operator

{% hint style="info" %}
Complete it by reading the man
{% endhint %}

### Execl / Execle

* Family of functions that execute  \(shell\) commands by replacing the current process with a new one
* `execl(<path_to_cmd_bin>, <bin_name>, <cmd_arg>, ..., NULL)`
  * The 1st arg is the path to the target cmd binary
  * Then comes the list of the command-line arguments, the first one being the name of the executable
  * A `NULL` terminates the arguments list
  * Uses the current environment variables
*  `execle(<path_to_cmd_bin>, <bin_name>, <cmd_arg>, ..., NULL, <env_array>)`
  * Similar to the other function, with an additional parameters to describe the environment variables to use
  * The environment is describe as an array of strings, terminated by a `NULL`

## Misc

* String are `const`, so you can't change their content
* Check [this article](https://matt.sh/howto-c), but it differs a lot from what we did in school without always really explaining why, so do as you wish.

## GCC

* Lets you compile C files
* `gcc <file_name> [-o <output_name]`
  * The default output name is `a.out`
* To compile in 32 bits, use this option : `-m32`
  * You might need to install this first : `libc6-dev-i386`

{% hint style="info" %}
Detail more
{% endhint %}


# JSONiq for Python users

This tutorial introduces JSONiq to Python users by directly comparing the languages.

What is presented here is not only the core JSONiq language, which is purely declarative and functional, but also its Update Facility extension, to modify JSON documents, as well as its Scripting extension, which supports imperative features such as variable assignment, side effects and while loops.

## Using JSONiq as a calculator

First, JSONiq can be used as a calculator like Python.

### Basic arithmetics

The basic arithmetic operations available are similar (+, *, -) except that the division is done with *div* rather than /. Integer division is done with *idiv* rather than //. 

Powers are done with the pow() function rather than **.

    2 + 2
    
    (50 - 5*6) div 4
    
    pow(10, 3)
    
### Number types

Like Python, JSONiq supports the entire set of integers. Literals such as 4 and 345 are of the integer type, corresponding to Python ints. Literals such as 4.3 and 345.235423 are of the decimal type, which supports the entire set of decimals, like Python Decimals. Literals in scientific notation such as 345.23e34 are of the double type, corresponding to Python floats.

Number types can be processed together at will, as conversions are automatically performed.

### Variable declaration and assignment (JSONiq Scripting feature)

Variables can be declared, initialized and subsequently assigned new values. A difference with Python is that variable names start with a dollar sign. Another is that JSONiq statements end with semi-colons, like in Java or C++.

    variable $i := 1;
    $i := $i + 1;
    
Variables can be, optionally, typed statically with the "as" keyword. An error will be thrown if the assigned value does not fit the type.

    variable $i as integer := 1;
    $i := $i + 1;
    variable $j as decimal := 2.3;
    $j := $j + 1;
    
In order to return/print a value, simply omit the last semi-colon (the last line is thus an expression, not a statement).

    variable $i as integer := 1;
    $i := $i + 1;
    variable $j as decimal := 2.3;
    $j := $j + 1;
    $j
    
## Strings

JSONiq strings are always enclosed with double quotes, not simple quotes.

    "This is a string"

Escaping is similar to Python and also JSON, with backslashes:

    "This is a newline\nand an escaped \"quote\"."
    
Strings are concatenated with the || operator or the concat() function. Sequences of strings can be concatenated with the string-join() function, possibly with a separator.

    "This is " || "a string"
    
    concat("This is ", "a string")
    
    string-join(("This", "is", "a", "string"), " ")
    
This also works with variables.

    variable $str := "This is ";
    $str := $str || "a string.";
    $str
    
JSONiq has a rich function library to manipulate strings:

    substring("String", 3, 1)
    
    string-length("String")
    
    uppercase("String")
    
    lowercase("String")
    
    contains("String", "tr")
    
    starts-with("String", "S")
    
    ends-width("String", "g")
    
    substring-before("String", "i")
    
    substring-after("String", "i")
   
    tokenize("This is a string", " ")
   
There are also regex functions such as matches and replace.

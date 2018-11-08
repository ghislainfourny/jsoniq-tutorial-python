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
    
Like in Python, strings are immutable. Changes to strings must be done by reassigning values to variables.
    
JSONiq has a rich function library to manipulate strings. Range slice indexes, in particular, use the substring() function in JSONiq.

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

## JSONiq Structures

### Sequences

The JSONiq counterpart of Python lists is probably sequences. Sequences are a first-class citizen in JSONiq, in the sense that any value, anywhere, returned or taken by an expression, is a sequence. Even if there is only one integer, or one string, it is still a sequence of one integer, or a sequence of one string.

Sequences can be built using comma separators. Sequences are so important in JSONiq that no special braces or brackets are needed. Surrounding them with parentheses is common though, in order to override precedence of expressions here and there.

    1, 2, 3, 4, 5, 6
    
or equivalently:

    (1, 2, 3, 4, 5, 6)

Even though integer ranges can be built faster with the "to" operator:

    1 to 10000
    
Looking up elements in a sequence by position is similar to python. In JSONiq we start counting at 1, i.e., the first item in a sequence is obtained with [1].

    variable $sequence := 1 to 10000;
    $sequence[10]
    
Slices can also be taken using the position() and last() functions:

    variable $sequence := 1 to 10000;
    $sequence[position() <= 10]

    (1 to 10000)[position() <= last() - 30]
    
Sequences are flat: concatenation is thus also performed with a comma operator (in Python, this would be a +):

    variable $sequence1 := 1 to 10000;
    variable $sequence2 := 1 to 10000;
    ($sequence1, $sequence2)

For example,

    ((1, 2, 3), (4, 5, 6))
    
is the same as

    1, 2, 3, 4, 5, 6
    
Sequences, like JSONiq strings, are immutable. Changes are always copied over to a new sequence. This is different from Python lists, which are mutable.

The size of a sequence can be accessed with the count() function:

    count(2 to 3000)
    
However, there are other aggregation operators available like sum(), avg(), min() and max():

    min(2 to 3000)

    max(2 to 3000)

    sum(2 to 3000)

    avg(2 to 3000)


### Arrays

JSONiq arrays are nothing else than JSON arrays. They are like sequences, except that they can nest and they are mutable.

Arrays are surrounded with square brackets, like in JSON.

    [ 1, 2, 3, 4, 5, 6 ]
    
    [ [ 1, 2, 3 ], 4, [ 5, 6 ] ]
    
Items can be looked up in an array with [[ ]], like so:

    variable $a := [ [ 1, 2, 3 ], 4, [ 5, 6 ] ];
    $a[[1]]

Note that if you use [1] (sequence lookup), this will output the array itself, because an array is the same as a sequence of one array, and the first element of that sequence is the array itself.

Any sequence, that is, the result of any JSONiq expression, can be "boxed" into a JSON array by surrounding it with square brackets like so:

    [ 1 to 2000 ]
    
Conversely, arrays can be "unboxed" to sequences with [] like so:

    variable $x as array := [1, 2, 3, 4, 5, 6]
    variable $y as integer+ := $x[]
    
Arrays can be updated in place using the JSONiq update facility:

    variable $x as array := [1, 2, 3, 4, 5, 6];
    insert json (3.1, 3.3, 3.7) into $x at position 4;
    append json (7, 8, 9) into $x;
    delete json $x[[2]];
    replace value of json $x[[2]] with 10;
    $x

### Objects

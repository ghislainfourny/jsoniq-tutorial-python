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

Warning: Rumble does *not* support the Scripting feature. Use let clauses instead (scroll down).

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
    
### Variable bindings

JSONiq is a functional language, which means that it also supports variable binding in a way similar to languages like Haskell and CAML. The code snippets from the previous paragraph can be rewritten as:

    let $i := 1
    let $i := $i + 1
    return $i
    
And with type declarations:

    let $i as integer := 1
    let $i := $i + 1
    let $j as decimal := 2.3
    let $j := $j + 1
    return $j
    
Variable bindings with let-return syntax are much preferrable to variable declaration and assignment, because more optimizations can be done by the underlying processor. There is another compelling reason: let-return constructs are themselves expressions, like arithmetics and logical expressions. Concretely, this means that they can be nested in further JSONiq expressions very easily:

    let $k :=
      count(
        let $i as integer := 1
        let $i := $i + 1
        let $j as decimal := 2.3
        let $j := $j + 1
        return 1 to $j
      )
    return $k * $k
    
In the rest of the tutorial, we will thus prefer the let-return construct to variable assignments, but keeping in mind that they are equivalent in spirit.

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

    let $str := "This is "
    let $str := $str || "a string."
    return $str
    
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

    let $sequence := 1 to 10000
    return $sequence[10]
    
Slices can also be taken using the position() and last() functions:

    let $sequence := 1 to 10000
    return $sequence[position() <= 10]

    (1 to 10000)[position() <= last() - 30]
    
Sequences are flat: concatenation is thus also performed with a comma operator (in Python, this would be a +):

    let $sequence1 := 1 to 10000
    let $sequence2 := 1 to 10000
    return ($sequence1, $sequence2)

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

Arrays are surrounded with square brackets, like in JSON -- actually, any well-formed JSON array can directly be copy-pasted into JSONiq.

    [ 1, 2, 3, 4, 5, 6 ]
    
    [ [ 1, 2, 3 ], 4, [ 5, 6 ] ]
    
Items can be looked up in an array with [[ ]], like so:

    let $a := [ [ 1, 2, 3 ], 4, [ 5, 6 ] ]
    return $a[[1]]

Note that if you use [1] (sequence lookup), this will output the array itself, because an array is the same as a sequence of one array, and the first element of that sequence is the array itself.

Any sequence, that is, the result of any JSONiq expression, can be "boxed" into a JSON array by surrounding it with square brackets like so:

    [ 1 to 2000 ]
    
Conversely, arrays can be "unboxed" to sequences with [] like so:

    let $x as array := [1, 2, 3, 4, 5, 6]
    let $y as integer+ := $x[]
    return $y
    
It is also possible to instead use the built-in function members():
    
    let $x as array := [1, 2, 3, 4, 5, 6]
    let $y as integer+ := members($x)
    return $y

The builtin function size() returns the length of an array

    let $x as array := [1, 2, 3, 4, 5, 6]
    return size($x)

Arrays can be updated in place using the JSONiq update facility and JSONiq scripting statements:

    variable $x as array := [1, 2, 3, 4, 5, 6];
    insert json (3.1, 3.3, 3.7) into $x at position 4;
    append json (7, 8, 9) into $x;
    delete json $x[[2]];
    replace value of json $x[[2]] with 10;
    $x

### Objects

JSONiq objects are nothing else than JSON objects: they map strings to any (single) value and these values can recursively be objects, arrays or atomics.

Arrays are surrounded with curly braces, like in JSON -- actually, any well-formed JSON object can directly be copy-pasted into JSONiq.

    { "foo" : "bar" }
    
    {
      "foo" : [ { "a" : 1, "b": 2 } ],
      "bar" : { "c" : true }
    }
    
Items can be looked up in an object with a dot, like in Python:

    let $a as object := {
      "foo" : [ { "a" : 1, "b": 2 } ],
      "bar" : { "c" : true }
    }
    return $a.bar.c
    
    let $a as object := {
      "foo" : [ { "a" : 1, "b": 2 } ],
      "bar" : { "c" : true }
    }
    return $a.foo[[1]].b
    

Any JSONiq expression can be dynamically put in lieu of a key or value. The result of that expression will be taken to create the JSON object:

    {
      string-join(("One", "Two"), "-") : [ 1 to 100],
      string-join(("One", "Two", "Three"), "-") : [ 1 to 100]
    }
    
As expected, an error will be raised if the expression used for a key does not return a single atomic item (which gets converted to a string implicitly). If the expression used for a value returns an empty sequence, then null is used as the value. If it returns a sequence of more than one item, it gets boxed inside an array, which is used as the value.

The builtin function keys() returns the keys of an object:

    let $x as object := { "foo" : true, "bar" : [ 1, 2, 3 ] }
    return keys($x)

Objects can be updated in place using the JSONiq update facility and JSONiq scripting statements:

    variable $x as object := { "foo" : "bar" };
    insert json { "bar" : true, "foobar" : 20 } into $x;
    delete json $x.foo;
    replace value of json $x.bar with false;
    $x


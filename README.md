# JSONiq for Python users

This tutorial introduces JSONiq to Python users by directly comparing the languages.

What is presented here is not only the core JSONiq language, which is purely declarative and functional, but also its Update Facility extension, to modify JSON documents, as well as its Scripting extension, which supports imperative features such as variable assignment, side effects and while loops.

## Using JSONiq as a calculator

First, JSONiq can be used as a calculator like Python.

The basic arithmetic operations available are similar (+, *, -) except that the division is done with *div* rather than /. Integer division is done with *idiv* rather than //. 

    2 + 2
    
    (50 - 5*6) div 4
    
Like Python, JSONiq supports the entire set of integers. Literals such as 4 and 345 are of the integer type, corresponding to Python ints. Literals such as 4.3 and 345.235423 are of the decimal type, which supports the entire set of decimals, with no correspondence in Python. Literals in scientific notation such as 345.23e34 are of the double type, corresponding to Python floats.

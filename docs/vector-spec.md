---
title: Vector Programming Language Specification
version: 0.1
date:
editor: Higenyi Tobias Thomas
---

## Introduction
This document describes the Vector Programming Language.
It focuses on Syntax and Semantics rather than implementation.
The current version described in this document is version 0.1.
The various sections in this document describe various aspects of
the language and how it is represented.
This document uses a simplified Backus-Naur Form (BNF) to express
language details.

### Description of Notation
The simplified Backus-Naur Form of expression used in this document is
described below. The notation is composed of productions.
Productions describe elements and entities in Vector.
They are made up of a name and a description separated by the `:=` symbol.
```
name := description
```
A description is composed of a combination of tokens,
symbols and productions themselves. Tokens are atomic building blocks 
of the language. They include keywords, literals, operators, separators
and white space. Descriptions are written as regular expressions.
If T is a term in a description D, then:
- T+      = T appears 1 or more times
- T*      = T appears 0 or more times
- T1|T2   = either T1 or T2
- (T1,T2) = a group of T1 and T2
- [T]     = T is optional

## Literals
Literals are a representation of supported builtin types in vector.
There are 4 literal types namely: integers, floats, strings and boolean
### Integers
Integers are numeric data represented by digits. Integers can be written
in binary, decimal and hexadecimal form. Binary numbers start with a `0(b|B)`
prefix followed by a sequence of 1 or 0 integers.
Decimal numbers are not prefixed. They contain a sequence of `0 - 9` integers.
Hexadecimal numbers start with a `0(x|X)` prefix followed by a sequence of
`0 - 9` digits and `a|A ... f|F` characters. The sequence of digits of a
number can optionally contain an underscore (`_`) character as a separator.
Decimal numbers are signed. Prefixing them with a minus sign (`-`) converts
them into negative numbers. Decimal numbers can also have an exponential
postfix represented by character (`e`) followed by a sequence of digits.
The sequence of digits can also be a negative number
```
binary-digit       := (0|1)
digit              := (binary-digit|2|3|4|5|6|7|8|9)
alpha-digit        := digit|`a`|`b`|`c`|`d`|`e`|`f`|`A`|`B`|`C`|`D`|`E`|`F`
binary-number      := 0(`b`|`B`) binary-digit ([`_`] binary-digit)*
decimal-number     := [-]digit ([`_`]digit)* [`e`[`-`]digit+]
hexadecimal-number := 0(`x`|`X`) alpha-digit ([`_`] alpha-digit)*
```
### Floating-point numbers
Floating-point numbers are numeric data represented by a sequence of digits
separated by a single period character (`.`). The sequence of digits on the
left-hand part is called the `whole number` while the sequence on the right 
is called `mantissa`. Floating point numbers are only represented with decimal 
integers. The mantissa can be postfixed with an exponent number.
Binary and Hexadecimal numbers cannot be used as floating-point numbers
```
floating-point-number := [-]digit+ `.` digit+[`e` digit+]
```
### String
A string is a sequence of characters enclosed in single or double 
quotation marks. The characters are from the unicode UTF-8 charset.
An empty string is one which DOES NOT contain a character sequence
between the quotation marks. Strings can be arbitrarily long.
```
character := u+0000 ... u+ffff
string    := (`"`|`'`) character* (`"`|`'`)
```
### Boolean
A boolean type is either true or false.
It is represented by values `true` and `false`.
```
boolean := `true`|`false`
```

## Scope
Scope is an execution context in which variables are defined.
They include:
- Module Scope
- Instance Scope
- Function Scope

## Identifiers
Identifiers are valid names for entities in a vector program.
They are sequences of alpha-numeric characters of variable length.
Identifiers SHOULD NOT start with numeric characters.
Identifiers CAN contain underscore characters (`_`).
Identifiers that start with an underscore character are considered
private to the current scope they are defined in.
Entities with Indentifiers that start with a double underscore(`__`)
are executed by the runtime in a special way
```
alpha-numeric-seq := character (character|digit)*
identifier        := [`_`[`_`]]alpha-numeric-seq
``` 

## Keywords
Keywords are words that carry special meaning in vector and can not
be used as identifiers. They include
```
def      if        else       switch
case     for       while      endif
endfor   endwhile  endswitch  end
return   yield     new        is
in
```

## Expressions

## Module

## Function
Functions are a sequence of statements enclosed in a scope. They have a signature
and a body. A function signature contains the `def` keyword used to define functions,
followed by the function name, a list of arguments and a return type. The function
definition can optionally contain modifiers. The function body contains a sequence
of statements and expressions. It is closed using the `end` keyword.

### Arguments
Arguments represent the number and type of input parameters that must be passed to
a function to execute it. They can be referred to as formal parameters. Arguments
can be divided into basic arguments, optional arguments and variadic arguments.
A function can only have a maximum of 8 arguments.

#### Basic Arguments
Basic arguments are arguments whose values need to be provided for the function to
execute. They are defined using a type and name.
```
basic-arg := type name
```

#### Optional arguments
Optional arguments are arguments whole values are not needed to be present for the
function to executed. They are defined as basic arguments but start with a minus (-)
prefix. The Optional argument can be provided with an optional value during definition
which it will assume in case a concrete value has not been passed to it during a call.
If an optional value is not provided, the default value of the type is assumed.
```
optional-arg := `-` basic-arg [`=` value]
```

#### Variadic arguments
Variadic Arguments take in input values of various numbers. Basic and Optional arguments
only take in a single value for each argument. Variadic arguments are defined as basic
attributes that are assigned to the Ellipsis (`...`) as its value. The number of values
that can be passed to a variadic argument range from 0 to infinity.
```
variadic-arg := basic-arg `=` `...`
```

#### Reference arguments
Reference arguments are used in pass-by-reference function calls. A reference is passed
as the value to the argument. It is defined as a basic argument but starts with an
asterick (`*`) as a prefix.
```
reference-arg = `*` basic-arg
```

### Function definition
```
function-definition := function-signature `:` function-body `end`
function-signature  := [modifier] `def` name `(`arguments`)` return-type
function-body       := statement+
arguments           := (basic-arg|optional-arg|variadic-arg)
```

### Function call
A function call is the application of a function to a set of parameters or input.
Parameters expressions that evaluate to a certain value. The parameters are passed 
in the order of arrangement of arguments in the function signature. A function call
is written with the name of the function followed by input parameters separated
by a comma and enclosed in parentheses. A function can also be called without
parameters if its signature doesnot define any arguments or if it defines optional 
arguments.
```
function-call := function-name`(` (expression,)* `)`
```

### Return statement
The return statement terminates the currently running function, places the return
value in the return address and returns execution back to the calling function.
If the calling function is the top-level executing function, the program is 
terminated. Return statements are written with the `return` keyword followed
by an expression. The expression must evaluate to a return value of the same type
as the return type in the function signature. A compile-time error is raised if
the returned value and return type in the function signature are of different types.
```
return-statement := `return` expression
```
### Yield statement


## Type
A type in vector is an entity that possesses attributes and functions that
perform operations on those attributes. A type is unique and distinguishable
from other types. A type can either be one of two kinds of types, which are;
primitive types or compund types. Primitive types are built into the programming
language while compound types can either be built into the language or defined
in vector code. Types defined in vector code are referred to as user-defined
types while types built into the language are referred to as builtin types

### Primitive Types
Primitive types are builtin vector datatypes that hold a single value in memory.
They are operated on by builtin operations. Builtin primitive types include:
- Integers
- Floats
- Strings
- Boolean
Primitive types are defined using their literal representation. The variable
storing the primitive type stores its actual value.

### Compound Types
Compound types are vector datatypes made by combining primitive or other
compound types. They can either be builtin or user-defined. The value storing
a compound datatype stores a reference to the type. The `new` keyword is used
to create a new instance of the compound type.

#### Builtin Compound Type
Builtin compound types include:
- List
- Dictionary

#### User-defined Compound Type
User-defined types are defined in code. They are created using the `type` keyword.
User-defined compound types include:
- Regular Type
- Generic Type
- Union Type

##### Regular Type
Regular types are defined using the `type` keyword followed by the type matter
and a list of attributes. The basic type matter only contains the name of the
newly created type. A type name is any valid identifier containing only characters
from the alphabet set. The type matter can also contain one optional modifier
placed before the type name. A newly created type can extend an existing type.
This is done by placing an arrow symbol `->` between the name of an existing type
and the name of a newly created type in the type matter.

##### Generic Type

##### Union Type

## Variables
Variables are memory locations that store addresses to values. They are written
with a type annotation first, followed by the variable name and then assigned to 
a value of the same type as the variable.
```
variable-definition := [modifier] type-annotation name "=" value
type-annotation     := type-definition
```

## Modifiers
Modifiers are special words that alter or enhance how an entity is
created or used. They include:
### const
The const modifier is used to define constant variables whose value is
unchanged throughout the life-time of the running program
### dataonly

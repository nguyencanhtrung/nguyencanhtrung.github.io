---
layout: distill
title: VHDL-1--Keywords and identifiers
description: Learn VHDL syntax
date: 2023-02-25
tags: vhdl
categories: en

authors:
  - name: Nguyen Canh Trung
    url: "https://bobibo.one"
    affiliations:
      name: bobibo.one

bibliography: 2018-12-22-distill.bib

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: 1. Installing Catapult and Questasim
  - name: 2. Installing Quartus
  - name: 3. Installing Vivado
  - name: 4. Adding these lines into `.bashrc`
  - name: 5. Starting Catapult

# Below is an example of injecting additional post-specific styles.
# If you use this post as a template, delete this _styles block.
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }

---


## Keywords and identifiers

#### What are the VHDL keywords? 

Keywords are the reserved words for language constructs and are used to denote a
function to the synthesis or compilation tool.

For example, `signal`, `variable`, and `function` are the keywords here.

#### What are VHDL identifiers? 

Identifiers are "nouns" that are used to describe various constructs within VHDL.

* An identifier cannot be a keyword. 
* The names given to signals, variables, function, procedure, and constants are
the identifiers; 
* For example: a, b, total, my buffer, etc.

Most of the time, designers prefer C style identifier as `C_styles_identifier`

Since the VHDL is case insensitive, `IDentiFier = identifier` 

Follow the following rules while writing an identifier:
* An identifier must start with a letter.
* It may contain letters, numbers, and underscores.
* No contiguous underscores are allowed
* The underscore may not end an identifier.
* The length of an identifier is specified by the provider of the synthesis or compilation tool.
* Infinite lenth is supported by the VHDL standard.

Note that the VHDL language internally converts all characters to UPPER CASE.



## Expressions and literals in VHDL

#### Expression
* Expression comprises of operator and operands.
    * The data objects form the operands of an expression
    * The data objects' value is used by the operators to perform some task.

For example:
* `Y <= A + B - C`
is a simple expression with operands A, B, C and operators `+` and `-`

* `M <= Y` is also an expression with a single identifier.

* `sig_hold <= func_or(a,b)` Also, a function call can be an expression.


#### Literal

A literal is a constant valued operand.
 
For example:
* `A <= '1'` - here A is a single bit literal
* `y <= "10000100"` - y is a std_logic_vector literal
* `CH <= 'A'`  - CH is a character literal

These examples illustrate unconditional signal assignments where the value on the right-hand side of
the assignment symbol (<=) is applied to the object on the left-hand side.

## Data objects in VHDL

Data object:

* Hold the value of a specific data type
* Used to pass values from one point to another
* Each data object can be assigned to one of the collections of finite values.

VHDL has four classes of data objects: 

* Constants
* Variables
* Signals
* Files.

#### Constants

* Can only hold one specific value of a specific data type for a given instance, and the value cannot be changed once declared
* The use of constants may:
    * Improve the readability of the VHDL code
    * Reduce the likelihood of making errors


#### Variables 

* Can hold any value of a specific data type 
* Used for <strong>storing temporary values</strong> inside a process or a function. 
* The value can be updated using a variable assignment statement
* A variable is declared within a <strong>block, process, procedure, or function</strong>, and is updated immediately when an assignment statement is executed.

 
#### Signal

* Hold a list of current and future values to be assigned that are to appear on the signal. The value can
be updated using a signal assignment statement.
* Each signal has one or more "drivers" which determine the value and timing of changes to the signal.
* Each driver is a queue of events which indicate when and to what value a signal is to be changed.
* Each signal assignment results in the corresponding event queue being modified to schedule a new event

#### Files

* The file refers to a system file which contains a value of the specified data type. 
* The file object here provides sequential access to system files where the values of a file are read or written sequentially


## VHDL object classes

There are four object classes that exist in VHDL in which certain qualities of the data object are identified:
* The `scalar` object class defines objects with a single value, defined indexes, and ordered structure.
* The `composite` object class defines group objects with either similar or different data types.
* The `access` object class defines pointers to the objects.
* The `file` object class defines a sequence of objects of a given data type

## Comments

The comments in any code can significantly enhance the readability of the code, provide useful documentation, and make the intent of the designer clear.

#### Comments rules

* VHDL comments always begin with a double dash ( `--` ) and end with a new line. 
* No multiline comments as in C/C++ (However, VHDL-2008 allows multi-line comments as `/* ... */` but it does not used much in implementation in industry due to lack of synthesizer supports)
* May begin anywhere in the line.
* End with the new line character.
* Any printable character may be included in a comment

### 5 levels of commenting in VHDL

* External documents
* File-level (header) comments
* Section or code group comments
* In-line comments
* Identifier-level comments

#### External documents

Any documentation not included, but cited, in the source module uses the external documents level. The detailed design document is the primary source, and the other likely sources are websites and textbooks.

#### File-level (header) comments
* Introduces the module with a file name and module contents. 
* Describes the inputs and outputs to or from the module
* Explains what the module does.

#### Section or code group comments

* Explains the purpose of a group of code

#### In-line comments

* Pinpoints the exact purpose of a statement of code.

#### Identifier-level Comments

* Good naming practices with these kinds of comments make the code more readable and less error prone.

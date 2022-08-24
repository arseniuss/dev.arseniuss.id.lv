---
layout: post
title:  "cellcomp: Notes from `test7.cell` in cell compiler development"
disqus: true
---

The `test7.cell` includes multiple aspects of the language.

1. Structure `string` including field `sub`: to check type generation.
2. Global variable `str` with default value - string: to check string assignment to structures.
3. Method using pointer to type.
4. Method using structure by value and returning by value.
5. Simple expression for variable `i`.
6. Local variable `test` with assigned string.
7. Complex expression with member/call chain for variable `ret`.
8. And returning member chain.

## Documentation

I had an idea to formalize documentation that errors can be tracked. Each section will have simple name (`cmds`, `tsys`, etc.), named sections and numbered rules. 

For example: decls/var/2: If `value` is not provided then compilator will assign default value of that `type`.

## Strings

Speaking of string assigment to structures, I'm assuming that a string has two properties:

 - `data`: pointer to null-terminated string;
 - `len`: the factual length of string.

These two properties are then applied to structures using metadata onto fields.
The null-termination will be useful for C interpolation but it won't be included in length.

## Pointers

I found out that there is some redundant structures. For example - pointer structure `cell_ptrnode`. By definition pointer is:

```
<pointer> ::= '*' <type>
```

You'd say "pointer of <type>". So why I do I need the `of` part to be a list? GONE!

## Arguments

When parsing various arguments such as local stack variables, function arguments etc. there was very many flags for generation. I just push all of then into one `ARG_*` in `cell/declarations.h` file. All them anyway uses structure `cell_vardecl` and are used similary.

## Functions

Very interesting aspect about functions is that in C structures bigger than word length (64 bits, sometimes 128 bits) are usually passed by pointer to functions. But If I pass them as pointer there will problems when function changes anything. Here the LLVM IR has useful attribute `byval` which creates buffer for passed structure. It may copy some memory when generating machine code but it won't mess the data.

Also since return values can be structures too I implemented logic to move return value to argument list with a flag `FUNC_VOID`. Also rewritten some function flags. I'll assume that last parameter will always be moved return value.

## Expressions

Expressions are hard to parse and generate. Most of the time I was just tweaking parsing and generation functions to get the result I want.

If we're speaking about operations, interesting thing is binary one. The problem with them is multiple parts:

```
var <variable> <variable type> = <left expression> <op> <right expression>
```

If we thing naivly then here is three parts, but actually it's four:

- left expression with values and type;
- right expressoin with value and type;
- target variable with value and type;
- AND(!) product of variable's value with value and type.

This really complicates things but it will be fun!

Speaking of member/call/index chains they are very challenging. I been writing order of instructions multiple times but somewhat algorythm didn't work as I wanted. I'd suggest to checkout `test7.cell` to understand that thing doesn't look as easy:

```
test.ext().len      | test.ext().len()

ext(test).len       | len(ext(test))
```

Try to draw graph of types and temporaries. That will be fun!

## Imports

For now I put that there are no standard library. But I haven't decided! I changed the code that all imports (mostly `builtin`) are optional but I also want to put standard library in version of _cell_ compiler written in _cell_.

## C

I wanted to use Linux's `list.h` header for lists but there wasn't a logic I needed: last entry etc. So I wrote mine `list.h`. I'll need to rename prefixes of functions!

## Compiler

I know that there are many aspects of code that have to refactored. The generation part have to just generate output without need for checks but since that affects progress I will do that later.

For now a good thing is to add in parsing stage everything to `unresolved` lists so middle-end can faster go through. The variadic functions here are very useful!

Also I have rewritten buffer handling everywhere using `safe_*` macros so buffer overflows are more easier to detect. The defaults in `cell/compiler.h` have changed.

Most interesting part is actually intermediate values `struct llvm_intervalue` which includes informaton about current operation: _cell_ type, LLVM type and LLVM temporary value. This idea came when I needed many components for instruction but I had to generate  each type. Now I just pass intermediate value with all types.

In structures from front-end and middle-end the intermediate values will be just in `void *data` field.

## Debugging

Note. It's annoying to count enumerator's number.

# Next plans

 - write binary tree implementation for symbol table to eliminate name collitions;

# Ideas

- Compiler have to be standard library independent.
- Tuples?
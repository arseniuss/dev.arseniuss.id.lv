---
layout: post
title:  "cellcomp: Notes from `test11.cell` in cell compiler development"
disqus: true
---

There is certain complexity when parsing and generating access paths. In the 11th test I implemented simple support for accessing array indexes.

Since access to structure fields and array indexes are the same there are many similarities and difficulties.

The LLVM IR instruction `getelementptr` is very simple for human but not so much for generating. The generator has to keep ference to first and last type to build instruction and also return all indexes in between.

In this test I'm testing only single dimention arrays but I hope there won't be much problems with multiple dimention arrays.

In same time array initialization is also an issue since I'm using the same function for expression parsing and I'm not planning use structure definition as tuples. I'll probably have to change that.

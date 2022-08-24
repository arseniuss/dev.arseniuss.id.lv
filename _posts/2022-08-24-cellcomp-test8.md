---
layout: post
title:  "cellcomp: Notes from `test8.cell` in cell compiler development"
disqus: true
---

I had an idea to allow multiple types of operators. Not only the known C operators like `%%` and `&&` but any combination of operator characters, ex. `!$%^&*` etc.

The idea come when I had weird issue with multiplication. Since in algebra there can be many types of multiplication I really wanted to show that in code.

```
i = j * k
vi = vj (*) vk
```

I'm still thinking about usefullness of this idea but I will keep it for now. I'm inspired by APL and I hope it won't go out of hand.

[Link to the test](https://github.com/arseniuss/flos-pcellc/blob/master/tests/functests/test8.cell)
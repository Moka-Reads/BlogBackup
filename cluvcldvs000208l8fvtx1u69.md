---
title: "TexCreate `translate-tex` Proposal"
datePublished: Thu Apr 11 2024 14:42:00 GMT+0000 (Coordinated Universal Time)
cuid: cluvcldvs000208l8fvtx1u69
slug: texcreate-translate-tex-proposal
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/xG8IQMqMITM/upload/e890dc17b4b621f06eb596f45115dea3.jpeg

---

TexCreate has recently had a surge in downloads, which makes me very happy to see. It is a nice motivation to keep going with the project, and one feature I'm proposing is one simple command to completely make the `texcgen` project obsolete.

While using the Zig programming language, one of my favorite compiler parts is the `translate-c` command, which allows it to read a C file and print out a Zig equivalent. Consider the following example:

```c
int add(int a, int b)
{
    return a + b;
}

int main()
{
    int c = add(3, 5);
    return 0;
}
```

If we were to use `zig translate-c foo.c`, we get the following output:

```c
pub export fn add(arg_a: c_int, arg_b: c_int) c_int {
    var a = arg_a;
    _ = &a;
    var b = arg_b;
    _ = &b;
    return a + b;
}
pub export fn main() c_int {
    var c: c_int = add(@as(c_int, 3), @as(c_int, 5));
    _ = &c;
    return 0;
}
```

This also has a bunch of bindings, but the core is here. So what if we had a tool to read a `.tex` file and generate a `json` file of it that is readable for `texcore`? This helps many individuals create custom Templates and perhaps even upload them to a centralized registry.

There is no actual code to this proposal yet, but it will be added to the Roadmap that I will build for TexCreate v4 so we can ensure that individuals have the best experience!
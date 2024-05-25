---
title: "The Road to TexCore `v1.0.0`"
datePublished: Sat May 25 2024 00:15:07 GMT+0000 (Coordinated Universal Time)
cuid: clwld01l300030al2bhzu1g0f
slug: the-road-to-texcore-v100
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/IUY_3DvM__w/upload/6eb6575cee4ce674464d9daf30247b9d.jpeg
tags: news, latex

---

I developed TexCore, our Rust crate to write Latex natively in Rust using Rust types. It is built to be simple: you add different Elements to a list, and it all joins together, and boom, you have a Latex document.

Currently, the library is at v0.7.2 and, at the time of writing, has 14,229 total downloads. However, I took a break from developing it further because the way the elements worked was becoming increasingly complex and less scalable. Certain problems just had workarounds but weren't practical. When you have a v1.0, the library is stable—a production-ready library!

So what is our road to this maturity is the following, and this involves having previous uses of this library pretty much be depreciated:

* Feature a full LaTeX AST (Abstract Syntax Tree)
    
    * This will feature simple ways to convert to LaTeX and Rust using `from()` and `to_string()` methods.
        
    * Be able to easily write complex LaTeX code in Rust using `from` to parse the string.
        
        * With a full error handling type, we also hope to catch syntax errors and report them when building a LaTeX document, and can be a way for TexCreate to verify a template.
            
* I will take inspiration from previous versions of simplifying some nodes by bundling them in a separate module.
    
* Improved documentation guide and a dedicated website (texcore.mokareads.org)
    
* We will be moving away TexCreate templates into a dedicated `texcreate_packager` crate that will take over the role of the `texcreate_templates` feature and `texc_repo` crate, and will be incharge of the package management role of the application.
    

This will be a lot of work, and I will need 2024 alone just for planning, along with my other commitments and TexCreate v4; I will honestly say there won't be progress for a while. I hope to get others involved with this project and make it easy for contributors to get involved with an issue system to pin the goals we need to complete.

I will create a new branch `dev_v1.0` that will be dedicated to the development of this. There's a lot of baggage to clear, a lot of ideas to figure out and iron out, but the dream is there, we will finally bring TexCore at `v1.0` as the first step of our massive TexCreate v4 update.
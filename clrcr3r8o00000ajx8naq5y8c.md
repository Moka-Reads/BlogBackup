---
title: "The MoKaReads Template"
datePublished: Sun Jan 14 2024 00:21:28 GMT+0000 (Coordinated Universal Time)
cuid: clrcr3r8o00000ajx8naq5y8c
slug: the-mokareads-template
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/bF2vsubyHcQ/upload/1f998c46469ad94af33c505739a4bf9d.jpeg
tags: templates, opensource, latex, creative-commons

---

Although this has been out for a while, I haven't discussed much about MoKa Template. Currently this is the core LaTeX template used in Zig Unleashed.

The aim of this template is to follow an elegant visual style that focuses on better readability and understanding for users by utilizing better program files graphics.

In the `v1.0.0-alpha` release, we include the following:

* Zig Language Support
    
* Terminal environment (`terminal`)
    
* Program file environment (`pfile` -&gt; file name, header color, icon)
    
* Prebuilt file environments:
    
    * Zig: `zigfile`
        
    * Rust: `rustfile`
        
    * C: `cfile`
        
    * C++: `cppfile`
        
* Exercise environment (`exercise`)
    
* Info environment (`info`)
    
* Warning environment (`warn`)
    

Each of these file graphics use rounded corners, header is filled with a colour to represent the language, as well as including an icon of the language.

This template is made for `a4` paper size, but you can edit it to go with whatever your requirements are, making sure to adjust the margin sizes.

The Zig Language support is created by us, and I will look into also distributing just the language support as a `.sty` file.

This template is under the Creative Commons Attribution-ShareAlike 4.0 International License so you are free to use it for your own uses as well as for commercial purposes!
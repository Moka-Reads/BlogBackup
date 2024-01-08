---
title: "TexCreate v4 Initial Discussion"
datePublished: Mon Jan 08 2024 05:00:27 GMT+0000 (Coordinated Universal Time)
cuid: clr4gff5j000109kte1nd8n49
slug: texcreate-v4-initial-discussion
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/8EzNkvLQosk/upload/46bf1e0e4b4e97f32cf87bdc8bef5d56.jpeg
tags: opensource, rust, planning, latex

---

In this article we hope to discuss the current shortfalls of v3, and how we hope to improve certain areas of TexCreate in v4.

> Background:
> 
> * TexCreate was created back in 2021 to serve one goal and one only, to create LaTeX projects using templates because I was lazy.
>     
> * The project has been growing ever since its initial version including separating various functions in different crates (packages in Rust) to help scalability. Version 3 contains more than 10,000 lines of code split apart in 8 crates.
>     
> * TexCreate v3 was very different from previous versions by removing the need to hardcode templates into the application, and instead locally storing templates using JSON files.
>     

Let's firstly discuss some of the positives or good direction that version 3 was heading with:

* Enabling first-party and custom templates.
    
* Creating a tool to create templates and generate `json`, `tex` versions of it utilizing the `texcore` library.
    

Here are some of the problems with our approaches:

* You kinda don't want to write a rust file to translate a LaTeX template
    
* Custom templates didn't have any management, unlike first-party templates.
    
* Application always checking for new versions of first-party template which would add latency to running `texcreate`
    

So in version 4, we hope to really work on improving our template and releasing system to better serve users. How do we hope to do this?

We hope to implement some sort of package manager builtin to TexCreate that can help `get`, `update`, and `remove` package lists. Let's take a look at what `$HOME/.texcreate` could look like (subject to change):

```bash
.texcreate/ 
    # this would contain a list of the packages 
    # such as names, github links, list of files in pkg
    package_lock.toml 
    # pgk1 contains three templates: foo, bar, baz
    pkg1/ 
        foo.json 
        bar.json
        baz.json 
```

With a package manager we hope to simplify the release process of templates, and better enable people to create their own templates for TexCreate. This can also enable a site to be created to record all the different packages that there are, and others can contribute by adding theirs in with a PR.

> We hope in v4, we won't need to completely rewrite TexCreate, and instead can still use most of the libraries, however with our new package manager approach, we will need to abandon `texc_v3_web` and only offer a CLI approach.

While I may not be able to start work on TexCreate anytime soon, I hope to continue working through plans in coming months and create more pseudo code and have less mysteries during development.

* [TexCreate on Crates.io](https://crates.io/crates/texcreate)
    
* [TexCreate Repos](https://github.com/mkproj/texc_v3_sources)
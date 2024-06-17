---
title: "Current Root Design Problems of QuickFetch"
datePublished: Mon Jun 17 2024 23:07:01 GMT+0000 (Coordinated Universal Time)
cuid: clxjl4wzf000309ju4c23fq1g
slug: current-root-design-problems-of-quickfetch
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/mxxagbVu7Pg/upload/646892cd70e13f8f795ebe3ce0acbfbf.jpeg
tags: news, rust

---

As I continue to work on QuickFetch, I want to highlight some root design issues that, once resolved, should, in theory, bring plenty of optimization benefits.

* Changing what's stored in key and values (this is just a rough idea, still needs to be worked out)
    
    * Key: Entry's name in bytes that is given from `E.name()`
        
    * Value: In a Value structure which contains the following:
        
        * `version`: From `E.version()`
            
        * `url`: From `E.url()`
            
        * `timestamp`: Will use `chrono::DateTime<UTC>::timestamp()`
            
        * `response`: The Response in bytes from `reqwest`
            

Some improvements that can come from changing the approach:

* Use `compare_swap` in `sled` that can do conditional modifications
    
* Use `contains_key` to check if the key already exists
    

What would need to change:

* `QFEntry` macro and the `Entry` trait
    
* How flexible we can think of Packages
    
* Changing the `channel_fetch` method to remove the constant timings it has between caching and already cached fetches.
    
    * This is caused by its need to make a request preemptively, but once we fix the following challenge, then fixing this should be simpler since we can utilize much faster methods to make a conditional request.
        
    
    Hopefully, this should take some time off the initial caching and subsequent fetches. We would need to change the respective handles for both methods after these changes, but it should result in a positive impact and simplified path.
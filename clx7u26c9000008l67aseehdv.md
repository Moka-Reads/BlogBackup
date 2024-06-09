---
title: "QuickFetch a Simple Package Manager Library"
datePublished: Sun Jun 09 2024 17:43:36 GMT+0000 (Coordinated Universal Time)
cuid: clx7u26c9000008l67aseehdv
slug: quickfetch-a-simple-package-manager-library
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/JwMGy1h-JsY/upload/fd0941b70a49a733b08c255ee93d380d.jpeg
tags: rust

---

In our quest to develop a package manager for the TexCreate project, I wanted to build a library to quickly be able to handle multiple asynchronous processes that use the same underlying client (using `reqwest` crate). I came up with the idea of developing `quickfetch`, which comes with the `Fetcher` structure that takes in a type that implements the trait `Entry`, and using it can take requests and cache them inside an embedded database using `sled` and can fetch it later for you in your local storage.

The Entry trait looks like the following:

```rust
pub trait Entry {
    fn is_modified(&self, keys_iter: impl DoubleEndedIterator<Item=Result<IVec, sled::Error>>) -> Option<IVec>;
    fn url(&self) -> String;
    fn entry_bytes(&self) -> Vec<u8>;
    fn log_cache(&self);
    fn log_caching(&self);
}
```

With this groundwork, we can expand into implementing a `Package` the structure which implements the `Entry` trait, and has the minimal requirements:

* name
    
* version (using semantic versioning verification)
    
* URL
    

We can then cache, update, and remove packages so that a user can hook this all up into a CLI.

There is still work to be done to make all of this functionality easy for the user, but a lot of the groundwork is already done, and we will announce the initial release with an article once it is completed.
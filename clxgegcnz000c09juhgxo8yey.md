---
title: "QuickFetch `v0.2` Released!"
datePublished: Sat Jun 15 2024 17:36:39 GMT+0000 (Coordinated Universal Time)
cuid: clxgegcnz000c09juhgxo8yey
slug: quickfetch-v02-released
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/HxKgk_MpHMM/upload/3cde2399e7edebb032feb5e05dc407fc.jpeg
tags: news, rust

---

QuickFetch `v0.2.0` is out, and it comes with many improvements!!! So, let's talk about the changes and how you can take advantage of them!

## Introducing Two New Crates!!!

In this release, we introduced two new crates, `quickfetch_traits` and `quickfetch_derive`, these two new libraries were essential to create to support procedural macros that will be essential for the library.

These are available directly in `quickfetch` and are re-exported as the modules `traits` and `macros` respectively.

Firstly in `traits` we have the `Entry` trait that we all love and know from the initial version, and nothing has changed to its signature.

Secondly in `macros` we have the `QFEntry` macro, which stands for QuickFetch Entry, and comes in the following signature you can see on [docs.rs](https://docs.rs/quickfetch_derive/0.1.0/quickfetch_derive/derive.QFEntry.html):

```rust
#[derive(QFEntry)]
{
    // Attributes available to this derive:
    #[mod_eq]
    #[mod_neq]
    #[url]
    #[name]
    #[version]
}
```

Let's see how `Package` looks now:

```rust
#[derive(QFEntry, Debug, Clone, Serialize, Deserialize)]
pub struct Package {
    #[mod_eq]
    #[name]
    name: String,
    #[mod_neq]
    #[version]
    version: String,
    #[mod_neq]
    #[url]
    url: String,
}
```

So...what do these attributes mean, which are required for `QFEntry` to work properly. Well let's start with the easiest ones, `#[name]` , `#[version]` and `#[url]`, these fields are used in the following methods,

* `log_cache()`, `log_caching()` is used with the format `{name} [{version}]`
    
* `url()` returns the string given in from the field which contains `#[url]`
    

The other attributes `mod_neq` and `mod_eq` relate to the `is_modified` function, and `eq` checks which ever fields should be equaled to find the entry from the iterator, and `neq` are the fields that shouldn't equal each other to see if a modification happened. So in `Package` the name should be equivalent, but the `version` and `url` should be different in order for a modification to be true.

## Channel Fetching

We have modified the `fetch` method to the following signature:

```rust
pub async fn fetch(&mut self, method: FetchMethod) -> Result<()>
```

Where `FetchMethod` is the following:

```rust
pub enum FetchMethod {
    Concurrent,
    Channel,
} 
```

In `v0.1`, we only had one option which was the concurrent method, this is still accessible using either `fetch(FetchMethod::Concurrent)` or `concurrent_fetch()`, while the new `Channel` method makes use of multi-producer single consumer bounded channels from the `tokio` crate that will send and receive tasks asynchronously up to the entries' array length. This method benefits from large number of requests being made, since we make use of `recv_many`, which makes this method faster than the concurrent!!!

To use this method, you can either use `fetch(Default::default())`, `fetch(FetchMethod::Channel)` or `channel_fetch()`.

## Other Changes:

* Made `Config` a generic structure with type `PK` that can be used for any structure which implements `Deserialize`
    
* Made `GithubReleaseBuilder` struct which can be used in configuration files, and can be then turned into `Package`, with the handy function `github_to_packages` that converts a `Vec<GithubReleaseBuilder` into `Vec<Package>`.
    
* `pairs(&self) -> Result<Vec<(E, Vec<u8>)>>` fetches and store all results from the database into a vector.
    
* `select(&self, key: E, op: SelectOp) -> Result<Option<Vec<u8>>>` allows you to perform the following operations with one function:
    
    * `Update`: Update the entry in the db (if successful returns `Ok(None)`)
        
    * `Delete`: Delete the entry in the db (if successful returns `Ok(None)`)
        
    * `Get`: Get the entry in the db (if successful returns `Ok(Some(Vec<u8>))`)
        
* `set_response_method(&mut self, response_method: ResponseMethod)`: Allows you to choose how you'd like to handle the response bytes, with the following options:
    
    * `Bytes`: Fetch the full response using the `bytes` method
        
    * `Chunk`: Fetch the response in chunks using the `chunk` method
        
    * `BytesStream`: Fetch the response in a stream of bytes using the `bytes_stream` method
        
* Made code a lot more DRY and I hope optimizations can be made to lower some of the times that each method takes.
    

## Improvements to be Made

* Currently the `Channel` method preemptively sends the request to get the bytes, even if there is no modification, thus if all entries are cached, some of that time is wasted.
    
* Simplify the `QFEntry` macro
    
* Work in parallel to speed up the storing phase
    

### Links

* Github: [https://github.com/Moka-Reads/QuickFetch](https://github.com/Moka-Reads/QuickFetch)
    
* Crates.io: [https://crates.io/crates/quickfetch](https://crates.io/crates/quickfetch)
    
* Docs.rs: [https://docs.rs/quickfetch/latest/quickfetch/index.html](https://docs.rs/quickfetch/latest/quickfetch/index.html)
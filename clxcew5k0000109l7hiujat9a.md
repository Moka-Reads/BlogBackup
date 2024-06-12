---
title: "QuickFetch `v0.1` Released!"
datePublished: Wed Jun 12 2024 22:37:52 GMT+0000 (Coordinated Universal Time)
cuid: clxcew5k0000109l7hiujat9a
slug: quickfetch-v01-released
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/TamMbr4okv4/upload/d593b68e2bae275b1547e8bcee084f17.jpeg
tags: news, opensource, rust

---

QuickFetch, a library made to be able to handle multi-requests asynchronously while also storing them in an embedded database to fetch later, has been released, with the current version being `v0.1.1`.

It is important to note that this library is still a work in progress and is not ready to be used for production yet. I hope to remove this warning when I am able to work on it in parallel with the TexCreate package manager to see where the gaps need to be covered in terms of functionality. I'd like to develop other methods of fetching (how about channels?) and measure different parts of it.

This version comes with many cool functionalities to help develop a Package Manager. Let's start with the main type, `Fetcher<E>`. This is a generic structure that expects a type `E` that implements the `Entry` trait. As we showed in our previous post, the trait has the following signature:

```rust
/// Entry trait that will be used to be able to fetch and cache data as the Key
pub trait Entry {
    /// Check if the entry is modified inside of the db's key iterator and return the old key
    fn is_modified(
        &self,
        keys_iter: impl DoubleEndedIterator<Item = Result<IVec, sled::Error>>,
    ) -> Option<IVec>;
    /// Return the url of the entry to send the `GET` request to
    fn url(&self) -> String;
    /// Return the entry serialized as bytes to be used as the key in the db
    fn entry_bytes(&self) -> Vec<u8>;
    /// Log that the entry is cached
    fn log_cache(&self);
    /// Log that the entry is being cached
    fn log_caching(&self);

    fn from_ivec(value: IVec) -> Self where Self:Sized;
}
```

This might seem complicated or confusing of why they're necessary, so let's go explain each of these methods one by one and how all of them are in some way incorporated:

* `is_modified`: This checks if an entry is modified given the iterator of keys which is found in the sled `db.iter().keys()`. If there is a modification, we have some type of `Some(ivec)`, which is the old key value; thus, we remove the entry using the old key, insert the new key, and cache the response as the value.
    
* `url:` We expect that the structure you are giving us requests to something. So, we need a URL to make the request.
    
* `entry_bytes`: The entry is represented in bytes; we recommend using `bincode` to serialize a structure, if you are using a pure URL format, then use `as_bytes().to_vec()` .
    
* `log_cache`: A message you'd like to print to inform the user their entry has been cached.
    
* `log_caching`: A message you'd like to print informs the user their entry is being cached.
    
* `from_ivec`: Convert an `IVec` value to an Entry value, if you are using a structure, consider using `bincode` to deserialize the `IVec` value in its bytes form.
    

To see a simple form of implementing this for a `String` consider the following:

```rust
impl Entry for String {
    fn is_modified(
        &self,
        _keys_iter: impl DoubleEndedIterator<Item = Result<IVec, sled::Error>>,
    ) -> Option<IVec> {
        None
    }

    fn url(&self) -> String {
        self.to_string()
    }

    fn entry_bytes(&self) -> Vec<u8> {
        self.as_bytes().to_vec()
    }

    fn log_cache(&self) {
        info!("{} (cached)", self.url())
    }

    fn log_caching(&self) {
        info!("{} caching", self.url())
    }

    fn from_ivec(value: IVec) -> Self where Self: Sized {
        String::from_utf8(value.to_vec()).unwrap()
    }
}
```

In `is_modified`, we return `None` because if a URL is modified, that's not the same URL, so thus it cannot be "modified" no matter what. The other methods are quite easy to understand.

However, if we wanted a more Package style, then consider the `Package` type in `quickfetch::package::Package` which comes in a very minimal form:

```rust
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct Package {
    name: String,
    version: String,
    url: String,
    is_gh: bool, // used to turn off semantic versioning check for github releases
}
```

We can then follow its `Entry` Implementation as the following:

```rust

impl Entry for Package {
    fn is_modified(
        &self,
        keys_iter: impl DoubleEndedIterator<Item = Result<IVec, sled::Error>>,
    ) -> Option<IVec> {
        for key in keys_iter {
            let key = key.unwrap();
            let pkg = Package::from_ivec(key.clone());
            if &pkg.name == &self.name && (&self.version != &pkg.version || &self.url != &pkg.url) {
                return Some(key);
            }
        }

        None
    }

    fn url(&self) -> String {
        self.url.to_string()
    }

    fn entry_bytes(&self) -> Vec<u8> {
        bincode::serialize(&self).unwrap()
    }

    fn log_cache(&self) {
        info!("{} v{} (cached)", &self.name, &self.version)
    }

    fn log_caching(&self) {
        info!("{} v{} caching", &self.name, &self.version)
    }

    fn from_ivec(value: IVec) -> Self where Self: Sized {
        bincode::deserialize(value.as_ref()).unwrap()
    }
}
```

Where in `is_modified` we are checking if the version or URL differs from a package with the same name. Now, with the `Config` type, which is essentially a list of packages, we can then implement the following example in `examples/mufiz_pkg.rs`:

```rust
use std::path::PathBuf;

use quickfetch::Fetcher;
use quickfetch::package::{Config, Package};
use quickfetch::pretty_env_logger;

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    pretty_env_logger::init();
    let config = Config::from_toml_file("examples/pkgs.toml").await?;

    let mut fetcher: Fetcher<Package> = Fetcher::new(&config.packages(), "mufiz")?;
    fetcher.fetch().await?;
    fetcher.write_all(PathBuf::from("pkgs")).await?;
    Ok(())
}
```

And `pkgs.toml` listed as the following:

```ini
[[packages]]
name = "mufiz-rpm-aarch64"
version = "0.6.0"
url = "https://github.com/Mustafif/MufiZ/releases/download/v0.6.0/mufiz-0.6.0-1.aarch64.rpm"
is_gh = true

[[packages]]
name = "mufiz-rpm-i386"
version = "0.6.0"
url = "https://github.com/Mustafif/MufiZ/releases/download/v0.6.0/mufiz-0.6.0-1.i386.rpm"
is_gh = true

[[packages]]
name = "mufiz-rpm-mips"
version = "0.6.0"
url = "https://github.com/Mustafif/MufiZ/releases/download/v0.6.0/mufiz-0.6.0-1.mips.rpm"
is_gh = true

[[packages]]
name = "mufiz-rpm-mips64"
version = "0.7.0"
url = "https://github.com/Mustafif/MufiZ/releases/download/v0.6.0/mufiz-0.6.0-1.mips64.rpm"
is_gh = true

[[packages]]
name = "mufiz-rpm-mips64el"
version = "0.6.0"
url = "https://github.com/Mustafif/MufiZ/releases/download/v0.6.0/mufiz-0.6.0-1.mips64el.rpm"
is_gh = true

[[packages]]
name = "mufiz-rpm-mipsel"
version = "0.6.0"
url = "https://github.com/Mustafif/MufiZ/releases/download/v0.6.0/mufiz-0.6.0-1.mipsel.rpm"
is_gh = true
```

If we run the example, setting `RUST_LOG=info`, we get the following:

```bash
$ RUST_LOG=info cargo run --example mufiz_pkg
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.07s
     Running `target/debug/examples/mufiz_pkg`
 INFO  quickfetch::package > mufiz-rpm-aarch64 v0.6.0 caching
 INFO  quickfetch::package > mufiz-rpm-i386 v0.6.0 caching
 INFO  quickfetch::package > mufiz-rpm-mips v0.6.0 caching
 INFO  quickfetch::package > mufiz-rpm-mips64 v0.7.0 caching
 INFO  quickfetch::package > mufiz-rpm-mips64el v0.6.0 caching
 INFO  quickfetch::package > mufiz-rpm-mipsel v0.6.0 caching
```

Where do we hope to go from here?

* Expanding functionality with different strategies
    
    * For example, automatically fetching by watching a `Config` file
        
* Better refine Github release packages
    
* See how to fetch in parallel when joining all asynchronous processes, which currently happens iteratively using `join_all`.
    

### Links

* Github: [https://github.com/Moka-Reads/QuickFetch](https://github.com/Moka-Reads/QuickFetch)
    
* Crates.io: [https://crates.io/crates/quickfetch](https://crates.io/crates/quickfetch)
    
* Docs.rs: [https://docs.rs/quickfetch/0.1.1/quickfetch/](https://docs.rs/quickfetch/0.1.1/quickfetch/)
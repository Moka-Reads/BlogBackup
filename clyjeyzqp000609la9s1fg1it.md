---
title: "QuickFetch `v0.3.0` Released!!!"
datePublished: Sat Jul 13 2024 00:54:10 GMT+0000 (Coordinated Universal Time)
cuid: clyjeyzqp000609la9s1fg1it
slug: quickfetch-v030-released
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/LjqARJaJotc/upload/a4e8cabc8a01a97b27e658204e420fac.jpeg
tags: opensource, rust

---

Close to a month ago, I wrote about the root problems I found in QuickFetch, which I felt I needed to handle before the complexity became too hard and vital to the design. So firstly, we removed one of the new crates, `quickfetch_derive`, and now we are using `quickfetch_traitsv0.2` which comes with changes that helped overcome these root problems and allows us to have a much more robust framework:

Consider the following traits:

```rust
pub trait Entry {
    type Key: EntryKey + Send + Sync;
    type Value: EntryValue + Send + Sync;

    fn key(&self) -> Self::Key;
    fn value(&self) -> Self::Value;
}

pub trait EntryKey: Display {
    fn bytes(&self) -> Vec<u8>;
    fn from_ivec(value: IVec) -> Self
    where
        Self: Sized;
    fn log_cache(&self) {
        info!("{} (cached)", self)
    }
    fn log_caching(&self) {
        info!("{} caching", self)
    }
}

pub trait EntryValue {
    /// Convert the value to bytes
    fn bytes(&self) -> Vec<u8>;
    /// Convert the value from IVec
    fn from_ivec(value: IVec) -> Self
    where
        Self: Sized;
    /// Return the url to send the request
    fn url(&self) -> String;
    /// Return the response as a Copy on Write byte array
    fn response(&self) -> Cow<'_, [u8]>;
    /// Set the response from the request as a byte array
    fn set_response(&mut self, response: &[u8]);
    /// Check if the value is the same as another value (excluding the response)
    fn is_same(&self, other: &Self) -> bool
    where
        Self: Sized;
}
```

As you can see, we split the Entry into two portions: a Key that needs to implement the `EntryKey` trait and a Value that needs to be implemented in the `EntryValue` trait where each has different needs.

Typically, for a key, we want it to be some name, so then that would be a `String`. Consider how we implemented it:

```rust
impl EntryKey for String {
    fn bytes(&self) -> Vec<u8> {
        self.as_bytes().to_vec()
    }

    fn from_ivec(value: IVec) -> Self
    where
        Self: Sized,
    {
        String::from_utf8(value.to_vec()).unwrap()
    }
}
```

Now, when it comes to different values for the keys, we need to go into the `key_val` module in `quickfetch`, then we can look at a simple example of how a value type can be made, and implement the `EntryValue` trait:

```rust
#[derive(Serialize, Deserialize, Debug, Clone)]
pub struct SimpleValue {
    version: String,
    url: String,
    response: Vec<u8>,
}
```

As you can see, this contains many crucial things we need to consider one entry different from the other, as well as the `response` to have the data that we fetched for.

Now, in this type, it is important to note that if the versions are different, then so should the URL.

```rust
impl EntryValue for SimpleValue {
    fn bytes(&self) -> Vec<u8> {
        bincode::serialize(&self).unwrap()
    }

    fn from_ivec(value: IVec) -> Self
    where
        Self: Sized,
    {
        bincode::deserialize(&value).unwrap()
    }

    fn url(&self) -> String {
        self.url.clone()
    }

    fn response(&self) -> Cow<'_, [u8]> {
        Cow::from(&self.response)
    }

    fn set_response(&mut self, response: &[u8]) {
        self.response = response.to_vec();
    }

    fn is_same(&self, other: &Self) -> bool {
        self.version == other.version
    }
}
```

As you can see, this is very simple and intuitive to implement.

We didn't achieve much of a performance boost with these changes, but they improved the robustness of the code greatly.

## Bars Bars Get Those Progress Bars

We now allow you to set the notification method you'd like to use, whether logging or progress bars. For obvious reasons, progress bars only support byte stream and chunk response methods.

To enable this, make the following adjustments:

```rust
// To enable progress bar for fetching
fetcher.set_notify_method(quickfetch::NotifyMethod::ProgressBar);
// Set the response method to BytesStream or Chunk for progress bars
fetcher.set_response_method(quickfetch::ResponseMethod::BytesStream);
```

For a demonstration of how it looks, consider this demo with the MufiZ Lang packages:

[![asciicast](https://asciinema.org/a/Ndm5wD4MG5dM0rCMG1IZi0CuE.svg align="left")](https://asciinema.org/a/Ndm5wD4MG5dM0rCMG1IZi0CuE)

## Watch Me!!!

The last amazing feature added is the watching ability. This allows you to edit the config file on the fly, and with the fetcher watching the config file, it will automatically refetch concurrently and log the fetching and events.

To enable this, use the following:

```rust
fetcher.watching().await?;
```

As always, these versions do break between each other as we refactor many things, so it's always good to refer to [docs.rs](https://docs.rs/quickfetch/latest/quickfetch/).
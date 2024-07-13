---
title: "How Each Fetching Method Works"
datePublished: Sat Jul 13 2024 16:57:30 GMT+0000 (Coordinated Universal Time)
cuid: clykddv8d000g0al85ugdcuw7
slug: how-each-fetching-method-works
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/LM17xCofKA0/upload/b914b54087efbb00fcfc5a083cd7ecda.jpeg
tags: opensource

---

It's always great to see at a high level how we can see the algorithms in work, as of the working version of `v0.4.0`, we have three methods of fetching, `Concurrent` which uses `tokio` green threads to handle the task of handling each entry, `Channel` which uses asynchronous bounded channels to send and receive this task and `Watch` which will keep running, watch for event changes in the configuration file, and use the concurrent fetch method to refetch the packages.

## Concurrent Method

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720889749312/2938e6ca-a809-4b1b-a518-5a12800be975.png align="center")

## Channel Method

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720889809359/dfd18123-efa0-46e8-b0cb-5296ece3de43.png align="center")

## Watch

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720889824659/354f1565-f4fe-41a7-9e22-e8f215e5b903.png align="center")
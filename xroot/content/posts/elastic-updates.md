---
title: "How Elastic handles data ..."
date: 2022-07-12T22:35:17+01:00
tags: [elastic,data]
draft: false
---

Someone once told me that one of the biggest problems with Elastic, and Elasticsearch, is how they frequently _"handle data as if it was a software"_, regarding updates and releases.

{{< figure src="/img/elastic-ffs.png" >}}

Something like this, apparently so non-intrusive _(and especially within a minor release)_, was all it took to bring an entire workflow down. 

I've learned to move away from Elasticsearch for anything that is mission-critical, but I keep running into changes that make me question if people are just sitting on old versions of the software terrified of the idea of updating.

---
title: Install Arbor
linktitle: Install Arbor
description: Install Arbor on Linux or Mac
date: 2016-11-01
publishdate: 2016-11-01
lastmod: 2018-01-02
categories: [getting started,fundamentals]
authors: ["@narendasan"]
keywords: [install,pc,windows,linux,macos,binary,tarball]
menu:
    docs:
        parent: "getting-started"
        weight: 30
weight: 30
sections_weight: 30
draft: false
aliases: [/overview/installing/,/getting-started/install,/install/]
toc: true
---


{{% note %}}
Its super easy to get started with Arbor
{{% /note %}}

Go is written in [Go](https://golang.org/) with and runs where Go does. 

#### Prerequisite Tools

* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Go (latest or previous version)](https://github.com/golang/tools/tree/master/cmd/getgo)


### Using Dependency Management (```dep```)

We recommend using Go's dependency management system to include ```arbor``` in your project

Install Dep the Go dependency manager (TODO link to instruction)

In your project run:

```
dep init
```
Then run 
```
dep ensure -add github.com/arbor-dev/arbor
```

#### Installing a Specific Version of ```arbor```
```
dep ensure -add github.com/arbor-dev/arbor[constraint]
```
Valid constraints include: 

- @0.2.4
    - Held at a v0.2.4 specifically

- \>=0.2.4
    - Held at or above v0.2.4

The complete set of vaild constraints can be found here: [Gopkg.toml Spec](https://github.com/golang/dep/blob/master/docs/Gopkg.toml.md)

## Quick Start
Now you are ready to get started creating your gateway. [Quick Start](/getting-started/quick-start)
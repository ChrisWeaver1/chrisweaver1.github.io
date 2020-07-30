---
layout: md
title: yuu
repo: mogolade/yuu
description: Create and use lightweight application clients for Githubs JSON API
tags: ['go', 'plugins', 'events']
sort_key: 1
---

{% include project-headers.html %}

![tests](https://github.com/mogolade/yuu/workflows/Go/badge.svg?branch=master) 
[![GoDoc](https://img.shields.io/static/v1?label=godoc&message=reference&color=blue)](https://pkg.go.dev/github.com/mogolade/yuu)

#### Yuu

Yuu is a lightweight module in its early stages of development/testing. Its aim is to aid in using plugin/event driven architecture within go. There are probably alternatives out there, I just started doing something small and it turned into this.

There are 2 main parts to this module, the `Plugin` and `Handler`. The `Handler` can register any `Plugin`'s it is asked to load, this can be through `.so` file(s) (built using `go build -buildmode=plugin`) or any struct that implements the `pkg/plugin.Plugin` interface.

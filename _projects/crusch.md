---
layout: md
title: crusch
repo: chrisweaver1/crusch
description: Create and use lightweight application clients for Githubs JSON API
tags: ['go', 'github', 'bot', 'api']
sort_key: 1
---

{% include project-headers.html %}

![tests](https://github.com/chrisweaver1/crusch/workflows/Go/badge.svg?branch=master) 
[![GoDoc](https://img.shields.io/static/v1?label=godoc&message=reference&color=blue)](https://pkg.go.dev/github.com/chrisweaver1/crusch)

Crusch is a lightweight libary which provides tools for Github Apps to communicate with Githubs V3 API, without too much unnecessary hassle.

This libary provides a simple client structure to make requests to Githubs API. Clients aid with adding and creating the required authorization headers, keeping track of when they might need to be renewed and other small helper methods.

Making a request is as simple as:
```go
client := crusch.NewDefault()
client.NewInstallationAuthFile(<ApplicationID>, <InstallationID>, <PEM keyfile location>)

var v []github.Issue{}
respose, err := client.GetJson("/repos/chrisweaver1/crusch/issues", 
    "assignee=chrisweaver1&state=open", &v)
```

It wasn't made a a replacement for the larger libaries that do similar things (`go-github`), but it was designed as a lightweight client to make thing easier for projects that don't need everything that `go-github` offers.
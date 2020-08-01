---
layout: md
title: crusch
repo: weavc/crusch
description: Authenticate & query Githubs API with crusch. Using crusch's lightweight clients to help manage authentication, you can easily make requests to Githubs API and automatically bind the result to a struct
tags: ['go', 'github', 'bot', 'application', 'api']
sort_key: 1
---

{% include project-headers.html %}

![tests](https://github.com/weavc/crusch/workflows/Go/badge.svg?branch=master) 
[![GoDoc](https://img.shields.io/static/v1?label=godoc&message=reference&color=blue)](https://pkg.go.dev/github.com/weavc/crusch)

Crusch is a lightweight libary which provides tools for Github Apps to communicate with Githubs V3 API, without too much unnecessary hassle.

This libary provides a simple client struct to make requests to Githubs API. Clients aid with adding and creating the required authorization headers, renewing them when they to be renewed and other small helper methods.

Making a request is as simple as:
```go
client := crusch.NewDefault()
client.NewInstallationAuthFile(<ApplicationID>, <InstallationID>, <PEM keyfile location>)

var v []github.Issue{}
respose, err := client.GetJson("/repos/weavc/crusch/issues", 
    "assignee=chrisweaver1&state=open", &v)
```

It wasn't made a a replacement for the larger libaries that do similar things (`go-github`), but it was designed as a lightweight client to make thing easier for projects that don't need everything that `go-github` offers.


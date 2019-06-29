---
layout: default
permalink: /p/mongo-sandbox
title: Mongo Sandbox Application
description: A MongoDB testing application, for testing queries, generating data and playing with Mongoose.
catagories: ['project']
customcss: /assets/css/markdown.css
image: /assets/images/posts/MongoDB_LogoStacked_FullColorBlack_RGB.jpg
tags: ['nodejs', 'typescript', 'mongodb', 'mongo', 'mongoose']
---

## MongoDB Sandbox Application

[![Generic badge](https://img.shields.io/badge/Github-mongo sandbox-blue.svg)](https://github.com/ChrisWeaver1/mongo-sandbox)

A [MongoDB](https://www.mongodb.com/) testing application, for testing queries, generating data and playing with [Mongoose](https://mongoosejs.com/docs/2.7.x/index.html). The application is written in Typescript/NodeJS and uses [Mongoose](https://mongoosejs.com/docs/2.7.x/index.html) to communicate with the database. [Mocha](https://mochajs.org/) is used to run the tests, allowing us to time and test different sets of queries. We can also set timeouts, fail certain tests based on the data that is bought back and all sorts of interesting and useful stuff. 

I threw this together in order to test a number of different queries for a project we were doing at work. We needed to see the performance on MongoDB's [$in selector](https://docs.mongodb.com/manual/reference/operator/query/in/) against large sets of data and arrays, which is what it is currently setup todo. However, I have used this for other projects as it's an easy way to setup and test different schemas, generate random data sets and even as a simple reference project. 

### Overview

- `generate.ts` 
    - generates random data and inserts it into the mongo database
    - run with `npm run generate`
- `tests.ts`
    - Test queries for Mongo database
    - Uses mocha for timings, success etc
    - run tests with `npm run test`
- `options.ts`
    - contains some configuration options like mongodb connection string etc
- `helpers/`
    - Just random data generation functions at the moment
- `db/`
    - Contains mongoose methods and schemas

### Quick Setup

```
git clone git@github.com:ChrisWeaver1/mongo-sandbox.git
cd mongo-sandbox
npm install
```


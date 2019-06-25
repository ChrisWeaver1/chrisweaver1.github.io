---
layout: default
permalink: /p/mongo-sandbox
title: Mongo Sandbox Application
description: A MongoDB testing application, for testing queries, generating data and playing with Mongoose.
catagories: ['project']
customcss: /assets/css/markdown.css
image: /assets/images/icons/mongodb.ico
tags: ['nodejs', 'typescript', 'mongodb', 'mongo', 'mongoose']
---

## Mongo Sandbox Application

[![Generic badge](https://img.shields.io/badge/Github-mongo sandbox-blue.svg)](https://github.com/ChrisWeaver1/mongo-sandbox)

A [MongoDB](https://www.mongodb.com/) testing application, for testing queries, generating data and playing with Mongoose. The application is written in Typescript/NodeJS and uses [Mongoose](https://mongoosejs.com/docs/2.7.x/index.html) to interface the database and [Mocha](https://mochajs.org/) for as a test framework to allow us to time and test different sets of queries.

I threw this togther in order to test a number of different queries for a project we were doing at work. We needed to see the performance on MongoDB's [$in selector](https://docs.mongodb.com/manual/reference/operator/query/in/) against large sets of data and arrays, which is what it is currently setup todo. However I have used this for other projects as it's an easy way to setup and test different schemas, generate random data sets and it provides a simple reference project. 

### Overview

- `generate.ts` 
    - generates random data and inserts it into the mongo database
    - run with `npm run generate`
- `tests.ts`
    - Test queries for mongo database
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
git clone git@github.com:ChrisWeaver1/mongoose-sandbox.git
cd mongoose-sandbox
npm install
```


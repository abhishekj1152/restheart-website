---
layout: docs
title: Roadmap
---

<div markdown="1" class="col-12 col-md-9 col-xl-8 py-md-3 bd-content">

{% include docs-head.html %} 

## RESTHeart 4.0

Expected release date: Q1 2019

## Security

RESTHeart 4.0 will delegate security to uIAM and will not embed security functions anymore. 

[uIAM](https://github.com/softInstigate/uiam) is a spin-off project from RESTHeart. It is a Identity and Access Manager that includes and extends the security features embedded in RESTHeart 3.x.

> uIAM will only focus on security while RESTHeart 4.0 will just provide a Web API for MongoDB in the spirit of having software bricks each of whom *does just one thing and does it well*. 

uIAM follows the same dual licensing scheme of RESTHeart.

## Representation format

We received many feedbacks asking for a simpler representation format and we have worked on it for  [mrest.io](https://mrest.io), the cloud RESTHeart service (currenlty in beta). 

> You can try the new format creating a free account at [mrest.io](https://mrest.io).

Starting with RESTHeart 4.0 the current plain json and HAL formats will be abandoned in favor of the following simpler one:

```bash
# list of dbs -> GET /

[
    "db_1", 
    "db_2", 
    ...,
    "db_n"
]

# list of collections of a db -> GET /db

[ 
    "collection_1", 
    "collection_2", 
    ...,
    "collection_n" 
]

# metadata of a collection  -> GET /db/_meta

{
    "args": [...]
    "checkers": [...],
    "transformers": [...],
    "feeds": [...]
}

# documents of a collection -> GET /db/coll
[ 
    { <doc_1> },
    { <doc_2> },
    ...,
    { <doc_n> }
]

# a document -> GET /db/coll/docid

{ 
    "prop_1": value,
    "prop_2": value,
    ...,
    "prop_n": value,
}
```

## Changes feeds with Websocket

RESTHeart 4.x will fully support [change streams](https://docs.mongodb.com/manual/changeStreams/index.html) introduced by MongoDB 3.6 for replica sets.

A new resource will be available, called `feed`. A feed can be created specifying a collection metadata that defines an aggregation and a URI in a similar way than an aggregation resource is currently defined.

The feed URI endpoint will open a *Websocket* for the client to be notified of updates on the aggregation result.

## Transactions

RESTHeart 4.x will fully support [multi document transactions](https://docs.mongodb.com/manual/core/write-operations-atomicity/#multi-document-transactions) introduced by MongoDB 4.0 for replica sets.

Following the REST paradigm, the approach to support transactions is modeling them as first class resources. 

The client can start a transaction:

```bash
#start a tx
POST /_transactions

HTTP/1.1 200 OK
Location: /_transactions/5bf58d909c5d125a2b9f0b86
```

Once a transaction has been created, requests can be executed under tis scope:

```bash
#create a document in the tx scope
POST /db/coll?txid=5bf58d909c5d125a2b9f0b86 {"a": 1}

HTTP/1.1 201 Created
```

The transaction can be committed or rolled back:

```bash
#commit tx 5bf58d909c5d125a2b9f0b86
PATCH /_transactions/5bf58d909c5d125a2b9f0b86 {"commit": true}

HTTP/1.1 204 No Content
```

```bash
#roll back tx 5bf58d909c5d125a2b9f0b86
DELETE /_transactions/5bf58d909c5d125a2b9f0b86

HTTP/1.1 204 No Content
```

## Plugin API changes

The java API for plugins (Transformers, Hooks, Checkers and Initializers) will undergo a refactoring aimed at simplify and cleaning the API with limited impacts on existing implementations.
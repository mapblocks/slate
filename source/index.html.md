---
title: MapBlocks API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - python
  - javascript

toc_footers:
  - <a href='https://mapblocks.ai/register'>Sign Up for a MapBlocks Account</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the [MapBlocks](https://mapblocks.ai) API. This documentation will hopefully allow you to understand both the core objects and relationships in MapBlocks as well as how you can interact with the API.


To setup your free account, head over here to [REGISTER](https://mapblocks.ai/register).

* Once you have your user account, you can start using our **browser-based IDE** immediately
* If you want to interact directly with the **API**, you can request your [API KEY](https://mapblocks.ai/account/request_api) here

We have documented language bindings for Shell, Python, and JavaScript! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.
<aside class="warning">TODO, code examples are not done, random/template Kittn API code is still included.</aside>


# Git vs MapBlocks Overview

One of the most useful, yet most **dangerous**, ways to understand the core of MapBlocks is to compare it to Git.  We say both "useful" and "dangerous" as it can provide a great entry-point for understanding, **BUT if you extend the analogy too far, you'll misconstrue the goals and powers of MapBlocks**.

## Similarities

* Primarily version control systems for code
* Multiple users can read, write, and edit code in shared or independent versions
* Ability to run, store, and access code either locally or remotely

## Differences
Git | MapBlocks
--------- | -------
Large projects with very long lifespans (complex software, full apps, etc)| Many independent, yet often related, projects. New projects are frequently 
A "pyramid" shape of code and versions. "Everything merges to Master" (eventually)| Highly iterative development and reusability/portability of code between related and unrelated projects.
Once a branch/commit has been merged, it is treated **primarily** as a "historical artifact" | Versions and Commits are treated as **evergreen** and can exist and be used simultaneously in the same project or across multiple projects.
The **Attomic Unit** (code) and it's **Lifecycle** are effectively considered to be "part of", and are "stored within", the Git structure | **Code** and **Lifecycle** are independent of **Storage/Structure** , allowing for dynamic relationships and (controlled) simultaneous versioning and usage across the MapBlocks structure.

## Rough Analogs

You can roughly decompose both **Git** and **MapBlocks** into their **Primary Objects** and their **Versioning/Lifecycle** attributes.

   | Git | MapBlocks
--------- | ------- | -----------
**Primary Objects** | `Repo`, `Folder`, `File`, `Line` | `Project`, (`SuperMap`), `Map`, `Block`, `Cell`, `Run`, `Parameter`, `Metric`
**Versioning/Lifecycle** | `Branch`, (`Version`), `Commit` | `Version`, `Commit`


### Primary Objects
For **Primary Objects**, you can draw the following rough relationships

Git | MapBlocks | Comments
--------- | ------- | -----------
`Repo` | `Project` | Both a `Repo` and `Project` function as a container to hold a collection of code to accomplish a purpose/goal.  The scope of the purpose/goal within MapBlocks is often smaller than Git.  
`Folder` | (`SuperMap`) | Within MapBlocks, the idea of a `Folder` is accomplished using a `SuperMap` (a `Map` that has other `Maps` as children, see `Map` below)
`File` | `Map` | Both `Files` and `Maps` function as "name spaced" **containers** for collections of code with related purposes.
none | `Block` | MapBlocks incorporates `Blocks` as intermediate "code container" (smaller than a file). While a `File` is a collection of code with related functionality; a `Block` is a smaller collection of code, generally with a narrow and specific function.  
`Line`| `Cell` | Both a `Line` and `Cell` are effectively the **atomic units** of code in their system and they are, in general, not expected to be fully functional on their own. Different than a `Line`, a `Cell` can be single or multi-line, and uniqueness / diffs are handled at the `Cell` level.  
none | `Run` | Functions as linkage between a **holistic snapshot** (group of linked commits) as well as a **container** for inputs (`Parameters`) and outputs (`Metrics`) for a single, unique full execution of a `Map`
none | `Parameter` | An **immutable** object to hold one specific input required for a `Run` of a `Map`
none | `Metric` | TODO

### Versions/Lifecycles
For **Versions/Lifecycles**, you can draw the following rough relationships

Git | MapBlocks | Comments
--------- | ------- | -----------
`Branch` | `Version` | In Git, a `Branch` is a "walled off" version of the **complete/full** `Repo` and, most often, its primary intent is to eventually "merge into master". In MapBlocks, a `Versions` can exist at multiple levels within the object hierarchy and only impact the scope of that hierarchy level.  MapBlock `Versions` do not have the implied goal of eventually "merging into master" (but that goal/effect can still be accomplished when needed).
`(Version)` | `Version` | In Git, a version is
`File` | `Map` | TODO
`Line`| `Block` and `Cell` | TODO
none | `Run` | TODO
none | `Parameter` | TODO
none | `Metric` | TODO

# MapBlocks Data Structure

MapBlocks utilizes [Graph] (https://en.wikipedia.org/wiki/Graph_(abstract_data_type)) structure to model all its data.  As a graph, it consists of **Nodes** and **Relationships**.  

A few notes on our specific graph implementation:

* Both **Nodes** and **Relationships** have **Labels** (types), and in MapBlocks a **single** Node instance can exist while having multiple (however Relationships only carry one specific Label)
* **Nodes** and **Relationship** have properties that store all their attributes.  Only a few **Relationships** in MapBlocks carry any properties (primarily an `index` property to allow for keeping the sort order of children nodes where applicable).


# Nodes and Relationships

Up next

# API: Authentication

> To authorize, use this code:


```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

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
Large, "walled off" projects with very long lifespans (complex software, full apps, etc)| Many independent, yet often related, projects. New projects are frequently created/completed and code/versions are often shared between multiple projects.
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
`Repo` | `Project` | Both a `Repo` and `Project` function as a container to hold a collection of code to accomplish a broader purpose/goal.  The scope of the purpose/goal within MapBlocks is often smaller than Git.  
`Folder` | (`SuperMap`) | Within MapBlocks, the idea of a `Folder` is accomplished using a `SuperMap` (a `Map` that has other `Maps` as children, see `Map` below)
`File` | `Map` | Both `Files` and `Maps` function as "name spaced" **containers** for collections of code with related purposes.
none | `Block` | MapBlocks incorporates `Blocks` as intermediate "code container" (smaller than a file). While a `File` is a collection of code with related functionality; a `Block` is a smaller collection of code, generally with a narrow and specific function.  
`Line`| `Cell` | Both a `Line` and `Cell` are effectively the **atomic units** of code in their system and they are, in general, not expected to be fully functional on their own. Different than a `Line`, a `Cell` can be single or multi-line, and uniqueness / diffs are handled at the `Cell` level.  
none | `Run` | Functions as linkage between a **holistic snapshot** (group of linked commits) as well as a **container** for inputs (`Parameters`) and outputs (`Metrics`) for a single, unique full execution of a `Map`
none | `Parameter` | An **immutable** object to hold one specific input required for a `Run` of a `Map`
none | `Metric` | In MapBlocks, an array of `time_step`:`value` pairs for a specific measurement/output associated with a `Run`.  

### Versions/Lifecycles
For **Versions/Lifecycles**, you can draw the following rough relationships

Git | MapBlocks | Comments
--------- | ------- | -----------
`Branch` | `Version` | In Git, a `Branch` is a "walled off" version of the **complete/full** `Repo` and, most often, its primary intent is to eventually "merge into master". In MapBlocks, a `Versions` can exist at multiple levels within the object hierarchy and only impact the scope of that hierarchy level.  MapBlock `Versions` do not have the implied goal of eventually "merging into master" (but that goal/effect can still be accomplished when needed).
`(Version)` | `Version` | In MapBlocks, a `Version` is a full-fledged Class with it's own unique properties/functions and it has parents/children/other relationships. In Git, a version is more akin to a simple label/tag assigned to a commit.
`Commit` | `Commit` | In both Git and MapBlocks, a `Commit` functions as a "snapshot" of a scope of code. The general "scope" in Git is an entire `Repo`, while in MapBlocks the "scope" is only for the specific `Object`/`Lifecycle` the `commit` is associated with.

# Data Structure Overview

MapBlocks utilizes [Graph] (https://en.wikipedia.org/wiki/Graph_(abstract_data_type)) structure to model all its data.  As a graph, it consists of **Nodes** and **Relationships**.  

A few notes on our specific graph implementation:

* All **Nodes** and **Relationships** have at least one **Label** (type)
* A **single Node** instance can have multiple/simultaneous labels, however **Relationships** only carry one specific Label
* Both **Nodes** and **Relationship** have properties that store all their attributes.


# Nodes

## BaseNode SuperClass
All nodes in MapBlocks inherit from the `BaseNode` class.

**`BaseNode(Node)`**

Property | Default | Description
--------- | ------- | -----------
uid | *auto_gen* | 32 character unique identifier
createdAt | *auto_gen* | DateTimeStamp of when the Node was created
lastUpdated | *auto_gen* | DateTimeStamp assigned on creation and on subsequent edits

## Dual Core and Lifecycle Nodes

<aside class="success">
<b>KEY CONCEPT:</b> Dual Inheritance of Core and Lifecycle Node Properties and Functions
</aside>
There are **3 Core** node types (Graph labels):  `Project`, `Map`, and `Block`. These Core Nodes co-exist with **3 Lifecycle** node types (also Graph labels): `Origin`, `Version`, and `Commit`. Every node of these types exist as **both** a **Core** type and a **Lifecycle** type.  An actual instance of these combined nodes (e.g. a `MapVersion`) will inherit the properties and functionalities from the both it's **Core** and **Lifecycle** class.

## Lifecycle Node Types
<aside class="success">
<b>KEY CONCEPT:</b> Lifecycle Progression
</aside>
1. From a **lifecycle** perspective:
  * `Origins` have `Versions` as children
  * `Versions` have `Commits` as children
2. When a **new** `Project`, `Map`, or `Block` is created from a blank slate, it starts as an `ProjectOrigin`, `MapOrigin`, or `BlockOrigin`. **However** from a nomenclature perspective, it is simply referred to as a `Project`, `Map` or `Block`. When initiated, an `Origin` instance automatically gets a `Version` (**v1**) lifecycle instance created and linked to the `Origin` instance.
3. All subsequent `Version` instances are created as children of the `Origin`.  From a nomenclature / interaction perspective, they are primarily handled using their **Lifecycle** label and the **Core** node type is inferred based on the parent. For example:
  * A `Map` (which is actually a `MapOrigin`) has the property `Map.versions`
  * `Map.Versions` is most often said to be an array of `Versions`, while it is more accurately an array of `MapVersions` (each having the properties of a `Map` and `Version`)

### Origin Nodes

**`Origin(BaseNode)`**

Property | Default | Description
--------- | ------- | -----------
lifecycle | "origin" | a static property to ensure tracking of **lifecycle** nodes across their multiple **core** node instances
last_child_num | *1*, *auto_gen* | Interger associated with the latest `Version.number` child created
versions | *relationships* | An array of `Version` children, ordered by thier `Version.number` ascending. Generated via one-to-many `HAS_VERSION` relationships between itself and `Version` nodes.
owner | *relationship* | A backlink `OWNS` relationship (*OwnerRel*) from the `User` who created the `Origin` node

### Version Nodes

**`Version(BaseNode)`**

Property | Default | Description
--------- | ------- | -----------
lifecycle | "version" | a static property to ensure tracking of **lifecycle** nodes across their multiple **core** node instances
number | *auto_gen* | the sequentially incremented sequence number that this specific `Version` is of it's parent `Origin`
last_child_num | *1*, *auto_gen* | Interger associated with the latest `Commit.number` child created
commits | *relationships* | An array of `Commit` children, ordered by thier `Commit.number` ascending. Generated via one-to-many `HAS_COMMIT` relationships between itself and `Commit` nodes.
owner | *relationship* | Directly inherited from it's parent `Origin` node


### Commit Nodes

**`Commit(BaseNode)`**

Property | Default | Description
--------- | ------- | -----------
message | "New Commit" | The **user submitted** commit message when saving a `Commit`
lifecycle | "commit" | a static property to ensure tracking of **lifecycle** nodes across their multiple **core** node instances
number | *auto_gen* | the sequentially incremented sequence number that this specific `Commit` is of it's parent `Version`
owner | *relationship* | Directly inherited from it's parent `Origin` node
<aside class="warning"><b>"Special Case Dual Nodes"</b>: See <b>Map</b> and <b>Block</b> for details of specific attributes unique to dual <b>MapCommit</b> and <b>BlockCommit</b> node instances</aside>




## CoreNodes (SuperClass and Children)

All **Core** node types inherit from the **`CoreNode`** class

### CoreNode Super Class

**`CoreNode(BaseNode)`**

Property | Default | Description
--------- | ------- | -----------
name | *required* | **User submitted** name. Must be unique for the **Core** node type for that **User**
description | *required* | **User submitted** description
notes | *optional* | A free-form text field that a user can use as a sub-title/sub-description/etc
todos | *optional* | An **array** of text strings that the user can add-to, edit, and delete-from.
public | *True* | A **boolean** flag defining if other MapBlock users can find and **read** the contents of this node.
 <aside class="warning"><b>"TODO"</b>: Define the Inheritance of <b>PUBLIC</b></aside>

### Project Node

**`Project(CoreNode)`**

 Property | Default | Description
 --------- | ------- | -----------
 core_type | "project" | A system assigned flag to aid in differentiation between **Core** types within **Lifecycle** nodes.
 maps | *relationships* | One-to-many `HAS_MAP` relationships linking the `Project` to `MapVersions`, linked with `HAS` relationships (*HasRel*)
 blocks | *relationships* | One-to-many `HAS_BLOCK` relationships linking the `Project` to `BlockVersions`, linked with `HAS` relationships (*HasRel*)
 users | *relationships* | One-to-many `HAS_USER` relationships (*PermissionRel*) linking the `Project` to `Users` with each relationship having it's own controllable permissions.

 <aside class="success">
 <b>KEY CONCEPT:</b> Notice that <b>Projects</b> are linked at the <b>Version</b> lifecycle stage (MapVersions and BlockVersions) as opposed to the <b>Origin</b> lifecycle stage (MapOrigins and BlockOrigins). <b>Maps/Blocks</b> at the <b>Origin</b> stage exist independent of a <b>Projects</b>.  
 </aside>


### Map Node

**`Map(CoreNode)`**

 Property | Default | Description
 --------- | ------- | -----------
 core_type | "map" | A system assigned flag to aid in differentiation between **Core** types within **Lifecycle** nodes.
 **MapCommit** | **--** | Below properties are **Only for MapCommits**  
 *blocks* | *relationships* | An ordered array of `BlockCommits` that are included in the `MapCommit`, linked with `HAS` relationships (*OrderedHasRel*)
 *runs* | *relationships* | An ordered array of `Runs` that have been executed using that `MapCommit`, linked with `HAS` relationships (*OrderedHasRel*)


### Block Node

**`Block(CoreNode)`**

 Property | Default | Description
 --------- | ------- | -----------
 core_type | "block" | A system assigned flag to aid in differentiation between **Core** types within **Lifecycle** nodes.
 **BlockCommit** | **--** | Below properties are **Only for BlockCommits**  
 *cells* | *relationships* | An ordered array of `Cells` that are included in the `BlockCommit`, linked with `HAS` relationships (*OrderedHasRel*)


## Supporting Nodes

### Cell Node

**`Cell(BaseNode)`**

 Property | Default | Description
 --------- | ------- | -----------
 cell_type | "code" | `string` identifier of either "code", "markdown", or "header"
 content | *required* | A `string` containing the user inputted contents in that `Cell`
 stdOut | `[]` | An array of strings.  Stores, in order, the system prints generated by `Cells` execution over time.
 lifecycle | "commit" | Cells are always considered to be at the a quasi `Commit` lifecycle object
 core_type | "cell" | A system assigned flag to aid in differentiation


### User Node

**`User(BaseNode)`**

  Property | Default | Description
  --------- | ------- | -----------
  userName | *required* | Unique nickname submitted by the user during account creation
  firstName | *required* | Submitted by the user during account creation
  lastName | *required* | Submitted by the user during account creation
  email | *required* | Submitted by the user during account creation
  *{Password}* | *required*, **NO ACCESS** | Hashed and salted password string, not exposed externally
  admin | False | `Boolean` flag providing Admin rights to the MapBlocks server instance

  ownsMaps | *relationships* | An array of `MapOrigins` linked to the user with `OWNS` relationships (*OwnerRel*)
  ownsBlocks | *relationships* | An array of `BlockOrigins` linked to the user with `OWNS` relationships (*OwnerRel*)

  ownsProject | *relationships* | An array of `Projects` linked **from** the user with `OWNS` relationships (*OwnerRel*)
  hasProjects | *relationships* | An array of `Projects` **back-linked** to the user with `HAS_USER` relationships (*PermissionRel*)


# Relationships

As mentioned above, MapBlocks utilizes directed relationships in its Graph structure.  This means each relationship has a **Start Node** (you could say "from" or "parent") and an **End Node** ("to" or "child").  Theoretically, in a directed graph, if a relationship exists "both ways", then it is modeled with 2 relationships, one in each direction.  

## Relationship Classes

MapBlocks uses two primary **Relationship Classes**, each with one additional sub-class:

- **HasRel(Relationship)**
  - Subclass **OrderedHasRel(HasRel)** simply adds an index property to handle ordering of children
- **PermissionRel(Relationship)**
  - Subclass **OwnerRel(PermissionRel)** simply sets all permissions to TRUE by default

These **Relationship Classes** are utilized to model the above documented *relationships* in **Nodes**. Each **Node** definition above details which **Relationship Class** it uses.


### HAS Relationship

**`HasRel(Relationship)`**`

Property | Default | Description
--------- | ------- | -----------
uid | *auto_gen* | 32 character unique identifier
createdAt | *auto_gen* | DateTimeStamp of when the Relationship was created


### ORDERED HAS Relationship (SubClass of HasRel)

**`HasRel(HasRel)`**`

Property | Default | Description
--------- | ------- | -----------
idx | *auto_gen* | an index **integer** that stores the order of linked children nodes


### PERMISSIONS Relationship

**`PermissionRel(Relationship)`**`

Property | Default | Description
--------- | ------- | -----------
uid | *auto_gen* | 32 character unique identifier
createdAt | *auto_gen* | DateTimeStamp of when the Relationship was created


### OWNER Relationship (SubClass of PermissionsRel)

**`OwnerRel(PermissionRel)`**`

Property | Default | Description
--------- | ------- | -----------
idx | *auto_gen* | an index **integer** that stores the order of linked children nodes



## Owners and Participants

At the `Origin` lifecycle level (the highest parent), which includes dual class `Maps`, `Blocks`,  `Projects`, only one user (the **Owner**) has access to the Node via a `OWNS` **Relationship**.  All other users  gain access and permissions at the `Version` level via a `Project` node. The `HAS_USER` relationship (direct from the `Project` to the other `Users`), and the specific permissions granted for each user is initially created by the `Project` **Owner** (depending on the permissions granted, the assigned user may then be able to invite other users, change permissions, etc).

**EXAMPLE:**

**2 `Users`** (one owner, one participant), **1 `Map`** that has **2 `Versions`**, and **1 `Project`** (one owner, one participant )

- **Owner Relationships**
  - *`Jim (User)`* **- `OWNS (PermissionRel: all permissions)` ->** *`Foo (Map)`*
  - *`FirstMap (Map)`* **- `HAS_VERSION (OrderedRel)` ->** *`Foo V1 (MapVersion)`*
  - *`FirstMap (Map)`* **- `HAS_VERSION (OrderedRel)` ->** *`Foo V2 (MapVersion)`*
  - *`Jim (User)`* **- `OWNS (PermissionRel: all permissions)` ->** *`OurProject (Project)`*

- **Project Relationships**
  - *`OurProject (Project)`* **- `HAS_MAP (OrderedRel)` ->** *`Foo V1 (MapVersion)`*
  - *`OurProject (Project)`* **- `HAS_USER (PermissionRel: editable)` ->** *`Bill (User)`*
- **Implications For Bill (participant)**
  - Has access to **Foo V1** (`MapVersion`) based on the `Project` - `HAS_USER` -> `User` relationship.  His specific **permissions** will have been defined by **Jim** (owner) when Jim created the `HAS_USER` relationship.
  - Will inherently know an `Origin` level of **Foo** (`Map`) exists, but he will have no access at that level
  - Will not be provided with any knowledge of, nor access to, the other **Foo V2** (`MapVersion`)
  - Bill's **permissions**, set by the **owner** Jim, for **OurProject** will **cascade down** to all of the `Versions`, `Commits`, `Cells`, etc that are linked to the `Project`



<aside class="warning"><b>STOP HERE</b> Everything below here is the template default still</aside>
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

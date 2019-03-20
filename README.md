![Ariadne](https://ariadne.readthedocs.io/en/master/_images/logo.png)

[![Documentation](https://readthedocs.org/projects/ariadne/badge/?version=master)](https://ariadne.readthedocs.io/)
[![Build Status](https://travis-ci.org/mirumee/ariadne.svg?branch=master)](https://travis-ci.org/mirumee/ariadne)
[![Codecov](https://codecov.io/gh/mirumee/ariadne/branch/master/graph/badge.svg)](https://codecov.io/gh/mirumee/ariadne)

- - - - -

# Ariadne

Ariadne is a Python library for implementing [GraphQL](http://graphql.github.io/) servers, inspired by [Apollo Server](https://www.apollographql.com/docs/apollo-server/) and built with [GraphQL-core-next](https://github.com/graphql-python/graphql-core-next).

The library already implements enough features to enable developers to build functional GraphQL APIs. It is also being dogfooded internally on a number of projects.

Documentation is available [here](https://ariadne.readthedocs.io/).


## Features

- Simple, quick to learn and easy to memorize API.
- Compatibility with GraphQL.js version 14.0.2.
- Queries, mutations and input types.
- Asynchronous resolvers and query execution.
- Subscriptions.
- Custom scalars and enums.
- Unions and interfaces.
- Defining schema using SDL strings.
- Loading schema from `.graphql` files.
- WSGI middleware for implementing GraphQL in existing sites.
- Opt-in automatic resolvers mapping between `camelCase` and `snake_case`.
- Build-in simple synchronous dev server for quick GraphQL experimentation and GraphQL Playground.
- Support for [Apollo GraphQL extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=apollographql.vscode-apollo).
- GraphQL syntax validation via `gql()` helper function. Also provides colorization if Apollo GraphQL extension is installed.


## Installation

Ariadne can be installed with pip:

    pip install ariadne


## Quickstart

The following example creates an API defining `Person` type and single query field `people` returning a list of two persons. It also starts a local dev server with [GraphQL Playground](https://github.com/prisma/graphql-playground) available on the `http://127.0.0.1:8888` address.

```python
from ariadne import (
    ObjectType, QueryType, gql, make_executable_schema, start_simple_server
)

# Define types using Schema Definition Language (https://graphql.org/learn/schema/)
# Wrapping string in gql function provides validation and better error traceback
type_defs = gql("""
    type Query {
        people: [Person!]!
    }

    type Person {
        firstName: String
        lastName: String
        age: Int
        fullName: String
    }
""")

# Map resolver functions to Query fields using QueryType
query = QueryType()

# Resolvers are simple python functions
@query.field("people")
def resolve_people(*_):
    return [
        {"firstName": "John", "lastName": "Doe", "age": 21},
        {"firstName": "Bob", "lastName": "Boberson", "age": 24},
    ]


# Map resolver functions to custom type fields using ObjectType
person = ObjectType("Person")

@person.field("fullName")
def resolve_person_fullname(person, *_):
    return "%s %s" % (person["firstName"], person["lastName"])

# Create executable GraphQL schema
schema = make_executable_schema(type_defs, [query, person])

# Run a dev server that includes GraphQL Playground
start_simple_server(schema) # Visit http://127.0.0.1:8888 to see the API explorer!
```

For more guides and examples, please see the [documentation](https://ariadne.readthedocs.io/).


Contributing
------------

We are welcoming contributions to Ariadne! If you've found a bug or issue, or if you have any questions or feedback, feel free to use [GitHub issues](https://github.com/mirumee/ariadne/issues).

For guidance and instructions, please see [CONTRIBUTING.md](CONTRIBUTING.md).

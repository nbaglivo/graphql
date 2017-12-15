@title[Introduction]

# GraphQL

Disclaimer: This is not a technical thing, just presents a problem and a possible solution. Also I'm not and expert and this need more work. 

---

@title[fundamental]


### This starts with a simple question:

In terms of what data matters... Who knows better? The server or the client?

---

@title[netflix]

Take netflix for example.

![GitHub Logo](https://tctechcrunch2011.files.wordpress.com/2015/05/screen-shot-2015-05-20-at-11-51-30-am.png)

---

@title[how it works]

### What if you could ask for what you need and get exactly that
Build a query and send it to the server.
The server gives you back a JSON.

![GitHub Logo](https://scontent-frx5-1.xx.fbcdn.net/v/t39.2365-6/11891339_452528061601395_1389717909_n.jpg?oh=7ca3501484468bba5f411384c7c40ebc&oe=5A9A203C)

---

@title[what's it]

### So what's GraphQL?

* An open spec for a flexible API layer.
* A data query language.

---

@title[lets_code]

### Let's code something

---

@title[setup_up]

### Setup the server

We're going to use Express

---

@title[setting_server]

```
import express from 'express';
import graphqlHTTP from 'express-graphql';
import mongoose from 'mongoose';

import schema from './graphql/schema';

var app = express();

// GraphqQL server route
app.use('/graphql', graphqlHTTP(req => ({
  schema,
  pretty: true
})));
```

@[2]
@[5]
@[9, 10, 11, 12, 13]


---

@title[schema_definition]

### GraphQL schema

A GraphQL schema is nothing else than a group of queries and mutations.

* Query: Things we can query for, dah.
* Mutations: Things that we can modify.

---

@title[schema_definition_code]

```
// ./graphql/schema.js
export default new GraphQLSchema({
  query: new GraphQLObjectType({
    name: 'Query',
    fields: {
      ...userQueries,
      ...todoQueries
    },
  }),
  mutation: new GraphQLObjectType({
    name: 'Mutation',
    fields: {
      ...userMutations,
      ...todoPostMutations
    },
  }),
});
```

@[2]
@[3, 4, 6, 7, 8]
@[9, 11, 12, 13, 14]

---

@title[schema_fields]

```
// ./schema/todo/queries.js
export {
  todo: {
    type: new GraphQLList(todoType),
    args: {
      id: {
        name: 'itemId',
        type: new GraphQLNonNull(GraphQLInt)
      }
    },
    resolve: (root, { id }, source, fieldASTs) => {
      let projections = getProjection(fieldASTs);
      return new Promise((resolve, reject) => {
          ToDoMongo.find({ id }, projections,(err, todos) => {
              err ? reject(err) : resolve(todos)
          });
      });
    }
  }
}

@[3]
@[4]
@[5]
@[6, 7, 8, 9, 10]
@[12, 13, 14, 15, 16, 17, 18, 19]
---.

@title[advantages]

### Advantages

* Avoid several roundtrips, you can get many resources in a single request.
* No need for ad-hoc endpoints.
* GraphQL uses types to ensure Apps only ask for what’s possible and provide clear and helpful errors.

---

@title[more_advantages]

### More advantages

* Evolve your API without versions (you can still deprecate things).
* Can be built on top of your existing infrastructure: REST, SOAP, existing databases, or anything else.
* A lot of tools.

---

@title[resources]

### Resources

* Four years of GraphQL: https://www.youtube.com/watch?v=zVNrqo9XGOs
* Learn GraphQL: http://graphql.org/learn/

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

@title[schema]

### GraphQL schema

A GraphQL schema is nothing else than a group of queries, mutations and subscriptions.

* Queries: Things we can query for, dah.
* Mutations: Things that we can modify.
* Subscriptions: Things that I can subscribe of (so I can get push messages and react instead of pooling)

---

@title[schema_definition]


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
```

@[3]
@[4]
@[5]
@[6, 7, 8, 9]
@[11, 12, 13, 14, 15, 16, 17, 18]

---

@title[schema_types]

### Types

* Are used to ensure Apps only ask for what’s possible.
* You can perform validation you can provide clear and helpful errors.
* You can derive them from Mongoose with some extra package. (probably not the best idea though)

---

@title[schema_types_definition]

```
// ./schema/todo/type.js
export default todoType = new GraphQLObjectType({
  name: 'todo',
  description: 'todo item',
  fields: () => ({
    id: {
      type: (GraphQLInt),
      description: 'The id of the todo.',
    },
    item: {
      type: GraphQLString,
      description: 'The name of the todo.',
    },
    completed: {
      type: GraphQLBoolean,
      description: 'Completed todo? '
    }
  })
});
```

---

@title[va;idation]

### Validation

If you perform an invalid query like this:

```
{
  todo {
    isCompleted // The real name is "completed"
  }
}
```

---

@title[va;idation_error]

You will get:

```
{
  "errors": [
    {
      "message": "Cannot query field \"isCompleted\" on type \"Todo\".",
      "locations": [
        {
          "line": 4,
          "column": 5
        }
      ]
    }
  ]
}
```

---

@title[versions]

### GraphQL has no version notion.

Whaaaat?

<img src="https://media.giphy.com/media/5TC1o3oRE68Mg/giphy.gif" width="200" height="300" />

---

@title[versions]

Suppose we have this

```
var UserType = new GraphQLObjectType({
  name: 'User',
  fields: {
    name: { 
      type: GraphQLString, 
      deprecationReason: 'We split up the name into two'
    },
    firstname: { type: GraphQLString },
    lastname: { type: GraphQLString }
  }
})
```

... and we want to deprecate the name field in favour of firstname and lastname

---

@title[versions]

We can do this:

```
var UserType = new GraphQLObjectType({
  name: 'User',
  fields: {
    name: { 
      type: GraphQLString,
      deprecationReason: 'We split up the name into two'
    },
    firstname: { type: GraphQLString },
    lastname: { type: GraphQLString }
  }
})
```

@[6](Add deprecationReason)
@[8](Add field firstname)
@[9](Add field lastname)

---


@title[demo]

### Quick demo time with GraphiQL

---

@title[advantages]

### Advantages

* Avoid several roundtrips, you can get many resources in a single request.
* No need for ad-hoc endpoints.
* Evolve your API without versions which makes devilering features faster
* Can be built on top of your existing infrastructure: REST, SOAP, existing databases, or anything else.
* A lot of tools.

---

@title[resources]

### Resources

* Four years of GraphQL: https://www.youtube.com/watch?v=zVNrqo9XGOs
* Learn GraphQL: http://graphql.org/learn/
* Case studies: https://www.graphql.com/case-studies/

---

@title[bonus_track]

### Bonus track: Angular integration with Apollo

<img src="https://media.giphy.com/media/l0MYHvlYiBUSxCYCY/giphy.gif" width="250" height="350" />

---

@title[bonus_track_code]

```
const FeedQuery = gql`
  query Feed {
    feed {
      createdAt
      score
    }
  }
`;

@Component({
  template: `
    <ul *ngFor="let entry of data | async">
      Score: {{entry.score}}
    </ul>
  `
})
class FeedComponent implements OnInit {
  data: Observable<any>;

  constructor(private apollo: Apollo) {}

  ngOnInit() {
    this.data = this.apollo.watchQuery({ query: FeedQuery })
      .valueChanges
      .map(({data}) => data.feed);
  }
}
```

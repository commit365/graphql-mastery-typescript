# Lesson 02: GraphQL Basics

In this lesson, we will explore the foundational concepts of GraphQL, including schemas, types, resolvers, queries, mutations, and subscriptions. We will also guide you through creating your first GraphQL server using Apollo Server. By the end of this lesson, you will have a solid understanding of how GraphQL operates and how to implement its core features.

## Understanding GraphQL Schema, Types, and Resolvers

### GraphQL Schema

A GraphQL schema defines the structure of your API. It specifies the types of data that can be queried and the relationships between those types. The schema acts as a contract between the client and the server, ensuring that both sides understand the data structure.

#### Key Components of a Schema

1. **Types**: GraphQL uses types to define the shape of the data. There are several types:
   - **Scalar Types**: Basic data types such as `String`, `Int`, `Float`, `Boolean`, and `ID`.
   - **Object Types**: Custom types that represent complex data structures. Each object type can have fields that correspond to other types.

2. **Queries**: The root `Query` type defines the entry points for fetching data. Each field in the `Query` type corresponds to a resolver function that retrieves the data.

3. **Mutations**: The root `Mutation` type defines the entry points for modifying data. Similar to queries, each field in the `Mutation` type corresponds to a resolver function that performs the modification.

4. **Subscriptions**: The root `Subscription` type allows clients to subscribe to real-time updates. This is useful for applications that require live data feeds.

### Example Schema Definition

Here’s a simple example of a GraphQL schema that defines a `Book` type and a `Query` type for fetching books:

```graphql
type Book {
  id: ID!
  title: String!
  author: String!
  publishedYear: Int
}

type Query {
  books: [Book!]!
  book(id: ID!): Book
}
```

### Resolvers

Resolvers are functions responsible for returning data for a specific field in the schema. Each field in a type must have a corresponding resolver function that fetches the data.

#### Resolver Function Signature

A resolver function typically receives four arguments:

- **parent**: The previous object, which for a field on the root `Query` type is often not used.
- **args**: An object that contains the arguments passed to the field in the GraphQL query.
- **context**: A value that is shared across all resolvers during a single request. It can hold authentication information, database connections, etc.
- **info**: Information about the execution state of the query, including the field name and the schema.

### Example Resolver

Here’s an example of a resolver for the `books` field in the `Query` type:

```typescript
const resolvers = {
  Query: {
    books: () => {
      return [
        { id: '1', title: 'GraphQL Basics', author: 'John Doe', publishedYear: 2021 },
        { id: '2', title: 'Advanced GraphQL', author: 'Jane Smith', publishedYear: 2022 },
      ];
    },
    book: (parent, args) => {
      const books = [
        { id: '1', title: 'GraphQL Basics', author: 'John Doe', publishedYear: 2021 },
        { id: '2', title: 'Advanced GraphQL', author: 'Jane Smith', publishedYear: 2022 },
      ];
      return books.find(book => book.id === args.id);
    },
  },
};
```

## Queries and Mutations: Fetching and Modifying Data

### Queries

Queries are used to fetch data from the GraphQL server. A query specifies which fields to retrieve and can include nested fields.

#### Example Query

Here’s an example of a query to fetch all books:

```graphql
query {
  books {
    id
    title
    author
  }
}
```

### Mutations

Mutations are used to modify data on the server. They are similar to queries but are used for creating, updating, or deleting data.

#### Example Mutation

Here’s an example of a mutation to add a new book:

```graphql
mutation {
  addBook(title: "New Book", author: "Alice Johnson", publishedYear: 2023) {
    id
    title
  }
}
```

### Implementing Queries and Mutations

To implement queries and mutations, you need to define them in your schema and provide corresponding resolver functions.

## Subscriptions for Real-Time Data

Subscriptions allow clients to receive real-time updates when data changes on the server. This is useful for applications that require live data, such as chat applications or collaborative tools.

### Setting Up Subscriptions

1. **Defining Subscription Types**: Add a `Subscription` type to your schema.

```graphql
type Subscription {
  bookAdded: Book
}
```

2. **Implementing Subscription Resolvers**: Set up a resolver that triggers when a new book is added.

### Example Subscription Resolver

```typescript
const { PubSub } = require('graphql-subscriptions');
const pubsub = new PubSub();

const resolvers = {
  Query: {
    // ...existing query resolvers
  },
  Mutation: {
    addBook: (parent, { title, author, publishedYear }) => {
      const newBook = { id: '3', title, author, publishedYear };
      pubsub.publish('BOOK_ADDED', { bookAdded: newBook });
      return newBook;
    },
  },
  Subscription: {
    bookAdded: {
      subscribe: () => pubsub.asyncIterator(['BOOK_ADDED']),
    },
  },
};
```

## Creating Your First GraphQL Server Using Apollo Server

Now that you understand the basics of GraphQL, let’s create your first GraphQL server using Apollo Server.

### Step-by-Step Guide

1. **Install Apollo Server**:
   ```bash
   npm install apollo-server graphql
   ```

2. **Create a New File**: Create a file named `server.js` in the root of your project.

3. **Set Up the Apollo Server**:
   ```javascript
   const { ApolloServer, gql } = require('apollo-server');

   // Define your schema
   const typeDefs = gql`
     type Book {
       id: ID!
       title: String!
       author: String!
       publishedYear: Int
     }

     type Query {
       books: [Book!]!
       book(id: ID!): Book
     }

     type Mutation {
       addBook(title: String!, author: String!, publishedYear: Int): Book
     }

     type Subscription {
       bookAdded: Book
     }
   `;

   // Define your resolvers
   const resolvers = {
     Query: {
       // Implement query resolvers
     },
     Mutation: {
       // Implement mutation resolvers
     },
     Subscription: {
       // Implement subscription resolvers
     },
   };

   // Create an instance of ApolloServer
   const server = new ApolloServer({ typeDefs, resolvers });

   // Start the server
   server.listen().then(({ url }) => {
     console.log(`🚀 Server ready at ${url}`);
   });
   ```

4. **Run Your GraphQL Server**:
   ```bash
   node server.js
   ```

5. **Access the GraphQL Playground**: Open your browser and navigate to `http://localhost:4000` to access the GraphQL Playground where you can test your queries and mutations.

## Conclusion

In this lesson, you have learned the foundational concepts of GraphQL, including schemas, types, resolvers, queries, mutations, and subscriptions. You have also created your first GraphQL server using Apollo Server. This knowledge sets the stage for more advanced topics in the upcoming lessons, where you will dive deeper into GraphQL's capabilities and best practices for building robust APIs with TypeScript. 

As you continue through this course, you will gain the skills necessary to build efficient, scalable, and maintainable applications using GraphQL and TypeScript.

[Next Lesson](./03_setting_up_a_graphql_server.md)
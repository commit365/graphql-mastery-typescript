# Lesson 03: Setting Up a GraphQL Server

In this lesson, we will guide you through the process of setting up a GraphQL server using Apollo Server with TypeScript. You will learn how to define your GraphQL schema, implement resolvers, connect to a database, and test your server using tools like GraphiQL and Postman. By the end of this lesson, you will have a fully functional GraphQL server that can handle queries and mutations.

## Creating a GraphQL Server with Apollo

### Step 1: Install Apollo Server

To begin, you need to install Apollo Server and GraphQL. Open your terminal and navigate to your project directory. Run the following command:

```bash
npm install @apollo/server graphql
```

This command installs the Apollo Server package along with the core GraphQL library.

### Step 2: Set Up Your Project Structure

Create a new directory for your server code. You can structure your project as follows:

```
/graphql-server
  ├── src
  │   ├── index.ts
  │   ├── schema.ts
  │   └── resolvers.ts
  └── package.json
```

### Step 3: Define Your GraphQL Schema

Create a file named `schema.ts` in the `src` directory. This file will contain your GraphQL schema definition.

**Example: src/schema.ts**
```typescript
import { gql } from '@apollo/server';

// Define your GraphQL schema
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
`;

export default typeDefs;
```

### Step 4: Implement Resolvers

Create a file named `resolvers.ts` in the `src` directory. This file will contain the resolver functions that handle the logic for your queries and mutations.

**Example: src/resolvers.ts**
```typescript
const books = [
  { id: '1', title: 'GraphQL Basics', author: 'John Doe', publishedYear: 2021 },
  { id: '2', title: 'Advanced GraphQL', author: 'Jane Smith', publishedYear: 2022 },
];

const resolvers = {
  Query: {
    books: () => books,
    book: (parent, args) => books.find(book => book.id === args.id),
  },
  Mutation: {
    addBook: (parent, { title, author, publishedYear }) => {
      const newBook = { id: String(books.length + 1), title, author, publishedYear };
      books.push(newBook);
      return newBook;
    },
  },
};

export default resolvers;
```

### Step 5: Create the Apollo Server Instance

Now, create the main server file named `index.ts` in the `src` directory. This file will set up the Apollo Server and connect it to the schema and resolvers.

**Example: src/index.ts**
```typescript
import { ApolloServer } from '@apollo/server';
import typeDefs from './schema';
import resolvers from './resolvers';

// Create an instance of ApolloServer
const server = new ApolloServer({ typeDefs, resolvers });

// Start the server
server.listen().then(({ url }) => {
  console.log(`🚀 Server ready at ${url}`);
});
```

### Step 6: Run Your GraphQL Server

To run your GraphQL server, you need to compile your TypeScript code. First, ensure you have TypeScript installed:

```bash
npm install --save-dev typescript ts-node @types/node
```

Next, create a `tsconfig.json` file in the root of your project to configure TypeScript:

**Example: tsconfig.json**
```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"]
}
```

Now, you can run your server using:

```bash
npx ts-node src/index.ts
```

### Step 7: Access the GraphQL Playground

Once your server is running, open your browser and navigate to `http://localhost:4000`. You will see the Apollo Sandbox, an interactive tool for testing your GraphQL queries and mutations.

### Step 8: Testing Your Queries

You can now execute GraphQL queries in the Apollo Sandbox. For example, to fetch all books, you can run the following query:

```graphql
query {
  books {
    id
    title
    author
    publishedYear
  }
}
```

To add a new book, you can run the following mutation:

```graphql
mutation {
  addBook(title: "New Book", author: "Alice Johnson", publishedYear: 2023) {
    id
    title
  }
}
```

### Conclusion

In this lesson, you have learned how to set up a GraphQL server using Apollo Server with TypeScript. You defined your GraphQL schema, implemented resolvers, and connected your server to handle queries and mutations. You also learned how to access the Apollo Sandbox to test your GraphQL API.

This foundational knowledge sets the stage for more advanced topics in the upcoming lessons, where you will explore additional features of GraphQL, best practices, and how to integrate GraphQL with modern web applications, including Next.js. As you progress through this course, you will gain the skills necessary to build robust, efficient, and scalable applications using GraphQL and TypeScript.

[Next Lesson](./04_creating_type_safe_graphql_queries_and_mutations.md)
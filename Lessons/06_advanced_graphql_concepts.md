# Lesson 06: Advanced GraphQL Concepts

In this lesson, we will explore advanced concepts in GraphQL that will enhance your understanding and ability to build robust APIs. We will cover implementing pagination and filtering, handling file uploads, using directives for conditional data fetching, and creating custom scalar types for enhanced data handling. By the end of this lesson, you will have a comprehensive understanding of these advanced features and how to apply them in your GraphQL applications.

## Implementing Pagination and Filtering in GraphQL

### Pagination

Pagination is a technique used to divide a large dataset into smaller, manageable chunks. It is essential for improving performance and user experience, especially when dealing with large collections of data.

#### Common Pagination Strategies

1. **Offset-Based Pagination**: This method uses an offset and a limit to fetch a specific subset of data. For example, to fetch the second page of results with a page size of 10, you would set an offset of 10.

   **Example Query**:
   ```graphql
   query GetBooks($offset: Int, $limit: Int) {
     books(offset: $offset, limit: $limit) {
       id
       title
       author
     }
   }
   ```

2. **Cursor-Based Pagination**: This method uses a cursor (a unique identifier) to fetch the next set of results. It is generally more efficient and can handle data changes better than offset-based pagination.

   **Example Query**:
   ```graphql
   query GetBooks($cursor: String) {
     books(after: $cursor) {
       id
       title
       author
       cursor
     }
   }
   ```

### Implementing Pagination in Your Schema

To implement pagination in your GraphQL schema, you can define your `Query` type to accept pagination arguments.

**Example: Schema Definition with Pagination**:
```graphql
type Query {
  books(offset: Int, limit: Int): [Book!]!
  paginatedBooks(cursor: String): BookConnection!
}

type BookConnection {
  edges: [BookEdge!]!
  pageInfo: PageInfo!
}

type BookEdge {
  node: Book!
  cursor: String!
}

type PageInfo {
  hasNextPage: Boolean!
  endCursor: String
}
```

### Resolvers for Pagination

In your resolvers, implement logic to handle pagination based on the provided arguments.

**Example: Resolvers with Pagination**:
```typescript
const resolvers = {
  Query: {
    paginatedBooks: async (_, { cursor }) => {
      const limit = 10; // Set your limit
      const query = cursor ? { _id: { $gt: cursor } } : {};
      const books = await Book.find(query).limit(limit);
      const hasNextPage = books.length === limit;

      return {
        edges: books.map(book => ({
          node: book,
          cursor: book._id,
        })),
        pageInfo: {
          hasNextPage,
          endCursor: hasNextPage ? books[books.length - 1]._id : null,
        },
      };
    },
  },
};
```

### Filtering

Filtering allows clients to request specific subsets of data based on certain criteria. This can be implemented alongside pagination.

**Example: Filtering Query**:
```graphql
query GetBooks($author: String) {
  books(author: $author) {
    id
    title
    author
  }
}
```

**Example: Resolvers with Filtering**:
```typescript
const resolvers = {
  Query: {
    books: async (_, { author }) => {
      const query = author ? { author } : {};
      return await Book.find(query);
    },
  },
};
```

## Handling File Uploads with GraphQL

Handling file uploads in GraphQL can be accomplished using the `graphql-upload` package, which provides a way to upload files as part of a GraphQL mutation.

### Step 1: Install `graphql-upload`

First, install the `graphql-upload` package:

```bash
npm install graphql-upload
```

### Step 2: Update Your Apollo Server Setup

Integrate the `graphql-upload` middleware into your Apollo Server setup.

**Example: Update `index.ts`**:
```typescript
import { ApolloServer } from '@apollo/server';
import { graphqlUploadExpress } from 'graphql-upload';
import typeDefs from './schema';
import resolvers from './resolvers';

// Create an instance of ApolloServer
const server = new ApolloServer({ typeDefs, resolvers });

// Start the server with upload middleware
server.listen().then(({ url }) => {
  console.log(`🚀 Server ready at ${url}`);
});

// Apply upload middleware
app.use(graphqlUploadExpress());
```

### Step 3: Define Your Mutation for File Uploads

Update your schema to include a mutation for file uploads.

**Example: Schema Definition for File Upload**:
```graphql
scalar Upload

type Mutation {
  uploadFile(file: Upload!): String!
}
```

### Step 4: Implement the File Upload Resolver

In your resolvers, implement the logic to handle file uploads.

**Example: File Upload Resolver**:
```typescript
const resolvers = {
  Upload: {
    // Define the Upload scalar type
  },
  Mutation: {
    uploadFile: async (_, { file }) => {
      const { createReadStream, filename } = await file;
      const stream = createReadStream();

      // Process the file (e.g., save it to disk, cloud storage, etc.)
      // For example, save to a local directory
      const path = `uploads/${filename}`;
      await new Promise((resolve, reject) => {
        const writeStream = fs.createWriteStream(path);
        stream.pipe(writeStream);
        writeStream.on('finish', resolve);
        writeStream.on('error', reject);
      });

      return `File uploaded: ${filename}`;
    },
  },
};
```

## Using Directives for Conditional Data Fetching

Directives in GraphQL allow you to modify the behavior of your queries and mutations. They can be used for conditional data fetching based on certain criteria.

### Built-in Directives

GraphQL has a couple of built-in directives:
- `@include`: Conditionally include fields based on a boolean argument.
- `@skip`: Conditionally skip fields based on a boolean argument.

### Example Usage of Directives

Here’s how you can use directives in a query:

```graphql
query GetBooks($includePublishedYear: Boolean!) {
  books {
    id
    title
    author
    publishedYear @include(if: $includePublishedYear)
  }
}
```

### Custom Directives

You can also create custom directives to implement more complex logic.

**Example: Custom Directive for Authorization**:
```graphql
directive @auth(requires: Role!) on FIELD_DEFINITION

enum Role {
  ADMIN
  USER
}

type Query {
  secretData: String @auth(requires: ADMIN)
}
```

### Implementing Custom Directives

In your resolvers, implement the logic for your custom directive.

**Example: Authorization Directive Implementation**:
```typescript
const { SchemaDirectiveVisitor } = require('graphql-tools');

class AuthDirective extends SchemaDirectiveVisitor {
  visitFieldDefinition(field) {
    const { resolve = defaultFieldResolver } = field;

    field.resolve = async function (...args) {
      const context = args[2];
      if (!context.user || context.user.role !== 'ADMIN') {
        throw new Error('Not authorized');
      }
      return resolve.apply(this, args);
    };
  }
}
```

## Creating Custom Scalar Types for Enhanced Data Handling

Custom scalar types allow you to define types that are not included in the standard GraphQL specification. This is useful for handling complex data formats, such as dates or JSON objects.

### Step 1: Define a Custom Scalar Type

In your schema, define a custom scalar type. For example, let’s create a `Date` scalar type.

**Example: Schema Definition with Custom Scalar**:
```graphql
scalar Date

type Book {
  id: ID!
  title: String!
  author: String!
  publishedDate: Date
}
```

### Step 2: Implement the Custom Scalar Type

In your resolvers, implement the logic for your custom scalar type.

**Example: Custom Scalar Implementation**:
```typescript
const { GraphQLScalarType } = require('graphql');

const DateScalar = new GraphQLScalarType({
  name: 'Date',
  description: 'A date string in the format of YYYY-MM-DD',
  parseValue(value) {
    return new Date(value); // Convert incoming integer to Date
  },
  serialize(value) {
    return value.toISOString(); // Convert outgoing Date to string
  },
  parseLiteral(ast) {
    return new Date(ast.value); // Convert hard-coded AST string to Date
  },
});

const resolvers = {
  Date: DateScalar,
  // ...other resolvers
};
```

## Conclusion

In this lesson, you have explored advanced GraphQL concepts, including implementing pagination and filtering, handling file uploads, using directives for conditional data fetching, and creating custom scalar types for enhanced data handling. 

These advanced features enable you to build more powerful and flexible GraphQL APIs that can cater to complex application requirements. As you continue through this course, you will gain deeper insights into best practices for integrating GraphQL with modern web applications, including Next.js, and learn how to optimize your APIs for performance and scalability.

[Next Lesson](./07_optimizing_graphql_performance.md)
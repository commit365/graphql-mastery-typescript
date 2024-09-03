# Lesson 05: Integrating GraphQL with Frontend Applications

In this lesson, we will explore how to integrate GraphQL with frontend applications, specifically using React and Apollo Client. We will cover the setup of Apollo Client in a React application, how to fetch data using GraphQL queries, how to perform mutations to modify data, and how to manage state effectively with Apollo Client. By the end of this lesson, you will have the skills necessary to connect your frontend application to a GraphQL API and handle data efficiently.

## Setting Up Apollo Client in a React Application

### Step 1: Install Required Packages

To get started, you need to install Apollo Client and its dependencies. Open your terminal and navigate to your React project directory. Run the following command:

```bash
npm install @apollo/client graphql
```

This command installs the Apollo Client library and the core GraphQL library required for making queries.

### Step 2: Create Apollo Client Instance

You need to create an Apollo Client instance that will connect to your GraphQL server. This is typically done in a separate file to keep your code organized.

1. Create a new file named `apolloClient.ts` in your `src` directory.

**Example: src/apolloClient.ts**
```typescript
import { ApolloClient, InMemoryCache, HttpLink } from '@apollo/client';

// Create an HTTP link to your GraphQL server
const httpLink = new HttpLink({
  uri: 'http://localhost:4000', // Replace with your GraphQL server URL
});

// Create an Apollo Client instance
const client = new ApolloClient({
  link: httpLink,
  cache: new InMemoryCache(), // Set up caching
});

export default client;
```

### Step 3: Wrap Your Application with ApolloProvider

To make the Apollo Client available throughout your React application, you need to wrap your application with the `ApolloProvider` component.

1. Open your `index.tsx` (or `index.js`) file and update it as follows:

**Example: src/index.tsx**
```typescript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import { ApolloProvider } from '@apollo/client';
import client from './apolloClient';

ReactDOM.render(
  <ApolloProvider client={client}>
    <App />
  </ApolloProvider>,
  document.getElementById('root')
);
```

## Fetching Data with GraphQL Queries in React Components

With Apollo Client set up, you can now fetch data using GraphQL queries in your React components.

### Step 1: Define Your GraphQL Query

Create a new file named `queries.ts` in your `src` directory to define your GraphQL queries.

**Example: src/queries.ts**
```typescript
import { gql } from '@apollo/client';

// Define a query to fetch all books
export const GET_BOOKS = gql`
  query GetBooks {
    books {
      id
      title
      author
      publishedYear
    }
  }
`;
```

### Step 2: Use the Query in a React Component

Now, create a new component named `BookList.tsx` that will use the `useQuery` hook to fetch and display the list of books.

**Example: src/BookList.tsx**
```typescript
import React from 'react';
import { useQuery } from '@apollo/client';
import { GET_BOOKS } from './queries';

const BookList: React.FC = () => {
  const { loading, error, data } = useQuery(GET_BOOKS);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <div>
      <h2>Book List</h2>
      <ul>
        {data.books.map((book: { id: string; title: string; author: string; publishedYear: number }) => (
          <li key={book.id}>
            {book.title} by {book.author} ({book.publishedYear})
          </li>
        ))}
      </ul>
    </div>
  );
};

export default BookList;
```

### Step 3: Include the BookList Component in Your App

Update your `App.tsx` file to include the `BookList` component.

**Example: src/App.tsx**
```typescript
import React from 'react';
import BookList from './BookList';

const App: React.FC = () => {
  return (
    <div>
      <h1>My GraphQL Bookstore</h1>
      <BookList />
    </div>
  );
};

export default App;
```

## Using GraphQL in API Routes for Server-Side Operations

In addition to fetching data on the client side, you can also use GraphQL in API routes for server-side operations. This is particularly useful for handling sensitive operations or when you need to perform server-side logic.

### Step 1: Create an API Route

If you are using Next.js, you can create an API route to handle GraphQL requests. Create a new file named `graphql.ts` in the `pages/api` directory.

**Example: pages/api/graphql.ts**
```typescript
import { ApolloServer } from 'apollo-server-micro';
import typeDefs from '../../src/schema';
import resolvers from '../../src/resolvers';

const server = new ApolloServer({ typeDefs, resolvers });

export const config = {
  api: {
    bodyParser: false,
  },
};

export default server.createHandler({ path: '/api/graphql' });
```

### Step 2: Fetch Data in API Route

You can now use this API route to fetch data on the server side and return it to the client. For example, you can create a new API route to fetch books.

**Example: pages/api/books.ts**
```typescript
import { NextApiRequest, NextApiResponse } from 'next';
import { ApolloClient, InMemoryCache, HttpLink } from '@apollo/client';
import { GET_BOOKS } from '../../src/queries';

// Create an Apollo Client instance
const client = new ApolloClient({
  link: new HttpLink({ uri: 'http://localhost:4000', fetch }),
  cache: new InMemoryCache(),
});

export default async function handler(req: NextApiRequest, res: NextApiResponse) {
  try {
    const { data } = await client.query({ query: GET_BOOKS });
    res.status(200).json(data);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch books' });
  }
}
```

## Managing State with Apollo Client

Apollo Client provides powerful state management capabilities, allowing you to manage local state alongside remote data seamlessly.

### Step 1: Using Apollo Client for Local State Management

You can use Apollo Client to manage local state by defining local-only fields in your GraphQL schema.

**Example: Adding Local State to Your Schema**
```graphql
type Query {
  isLoggedIn: Boolean!
}
```

### Step 2: Using Local State in Your Components

You can use the `useQuery` and `useMutation` hooks to manage local state in your components.

**Example: Managing Local State in a Component**
```typescript
import React from 'react';
import { useQuery, useMutation, gql } from '@apollo/client';

const IS_LOGGED_IN = gql`
  query IsLoggedIn {
    isLoggedIn @client
  }
`;

const TOGGLE_LOGIN = gql`
  mutation ToggleLogin {
    toggleLogin @client
  }
`;

const LoginStatus: React.FC = () => {
  const { data } = useQuery(IS_LOGGED_IN);
  const [toggleLogin] = useMutation(TOGGLE_LOGIN);

  return (
    <div>
      <p>User is {data.isLoggedIn ? 'logged in' : 'logged out'}</p>
      <button onClick={toggleLogin}>Toggle Login Status</button>
    </div>
  );
};

export default LoginStatus;
```

## Conclusion

In this lesson, you learned how to integrate GraphQL with frontend applications using Apollo Client in a React environment. You set up Apollo Client, fetched data with GraphQL queries, performed mutations to modify data, and managed local state effectively. This foundational knowledge is essential for building robust and efficient applications that leverage the power of GraphQL.

As you continue through this course, you will explore more advanced features of GraphQL, best practices for integration, and how to optimize your applications for performance and scalability. With these skills, you will be well-equipped to build modern web applications that utilize GraphQL and TypeScript effectively.

[Next Lesson](./06_advanced_graphql_concepts.md)
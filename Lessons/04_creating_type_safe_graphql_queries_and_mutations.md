# Lesson 04: Creating Type-Safe GraphQL Queries and Mutations

In this lesson, we will focus on creating type-safe GraphQL queries and mutations using TypeScript. Type safety is crucial in ensuring that your GraphQL API is robust and free from runtime errors. We will cover how to use TypeScript with GraphQL, generate TypeScript types from your GraphQL schema, write type-safe queries and mutations, and handle responses and errors effectively. By the end of this lesson, you will have a solid understanding of how to leverage TypeScript to enhance your GraphQL development experience.

## Using TypeScript with GraphQL

TypeScript is a superset of JavaScript that adds static typing to the language. This feature helps catch errors at compile time rather than at runtime, leading to more reliable code. When working with GraphQL, TypeScript can be used to define types for your schema, queries, and mutations, ensuring that your API is consistent and type-safe.

### Benefits of Using TypeScript with GraphQL

1. **Type Safety**: Ensures that the data you send and receive conforms to the defined types, reducing the likelihood of errors.
2. **Intellisense and Autocompletion**: Provides better development experience with IDE support, including autocompletion and inline documentation.
3. **Easier Refactoring**: Changes to types are easier to manage, as TypeScript will highlight where changes need to be made across your codebase.

## Generating TypeScript Types from GraphQL Schema Using `graphql-code-generator`

To take full advantage of TypeScript's type safety, you can generate TypeScript types from your GraphQL schema automatically. This process eliminates the need to define types manually and ensures that your types are always in sync with your schema.

### Step 1: Install `graphql-code-generator`

First, you need to install the necessary packages. Run the following command in your project directory:

```bash
npm install --save-dev @graphql-codegen/cli @graphql-codegen/typescript @graphql-codegen/typescript-operations @graphql-codegen/typescript-react-apollo
```

### Step 2: Create a Codegen Configuration File

Create a configuration file named `codegen.yml` in the root of your project. This file will define how the code generator should operate.

**Example: codegen.yml**
```yaml
schema: "http://localhost:4000" # URL of your GraphQL server
documents: "src/**/*.graphql"      # Path to your GraphQL queries and mutations
generates:
  src/generated/graphql.ts:         # Output file for generated types
    plugins:
      - "typescript"
      - "typescript-operations"
      - "typescript-react-apollo"
```

### Step 3: Run the Code Generator

Add a script to your `package.json` to run the code generator:

```json
"scripts": {
  "generate": "graphql-codegen"
}
```

Now, run the code generator with the following command:

```bash
npm run generate
```

This command will generate TypeScript types based on your GraphQL schema and any queries or mutations you have defined.

## Writing Type-Safe Queries and Mutations

With your types generated, you can now write type-safe queries and mutations in your application.

### Example Query

Suppose you have a GraphQL query to fetch all books:

```graphql
query GetBooks {
  books {
    id
    title
    author
    publishedYear
  }
}
```

You can use the generated types to create a type-safe query in your React component.

**Example: Using the Query in a React Component**
```typescript
import { useQuery } from '@apollo/client';
import { GetBooksQuery, GetBooksQueryVariables } from '../generated/graphql';
import GET_BOOKS from '../graphql/queries/getBooks.graphql'; // Assuming you have this query defined in a .graphql file

const BookList: React.FC = () => {
  const { loading, error, data } = useQuery<GetBooksQuery, GetBooksQueryVariables>(GET_BOOKS);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <ul>
      {data?.books.map(book => (
        <li key={book.id}>
          {book.title} by {book.author} ({book.publishedYear})
        </li>
      ))}
    </ul>
  );
};

export default BookList;
```

### Example Mutation

Now, let’s define a mutation to add a new book:

```graphql
mutation AddBook($title: String!, $author: String!, $publishedYear: Int) {
  addBook(title: $title, author: $author, publishedYear: $publishedYear) {
    id
    title
  }
}
```

You can use the generated types to create a type-safe mutation in your React component.

**Example: Using the Mutation in a React Component**
```typescript
import { useMutation } from '@apollo/client';
import { AddBookMutation, AddBookMutationVariables } from '../generated/graphql';
import ADD_BOOK from '../graphql/mutations/addBook.graphql'; // Assuming you have this mutation defined in a .graphql file

const AddBookForm: React.FC = () => {
  const [addBook, { loading, error }] = useMutation<AddBookMutation, AddBookMutationVariables>(ADD_BOOK);

  const handleSubmit = async (event: React.FormEvent) => {
    event.preventDefault();
    const title = event.currentTarget.title.value;
    const author = event.currentTarget.author.value;
    const publishedYear = parseInt(event.currentTarget.publishedYear.value);

    await addBook({ variables: { title, author, publishedYear } });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="title" placeholder="Title" required />
      <input name="author" placeholder="Author" required />
      <input name="publishedYear" placeholder="Published Year" type="number" />
      <button type="submit" disabled={loading}>Add Book</button>
      {error && <p>Error: {error.message}</p>}
    </form>
  );
};

export default AddBookForm;
```

## Handling Responses and Errors in TypeScript

When working with GraphQL queries and mutations, it's essential to handle responses and errors gracefully.

### Handling Responses

When you receive data from a query or mutation, you can use TypeScript to ensure that the data conforms to the expected types. This helps catch potential issues at compile time.

### Handling Errors

Both the `useQuery` and `useMutation` hooks provide an `error` object that you can use to display error messages to the user. Always check for errors after executing a query or mutation.

### Best Practices for Error Handling

1. **Display User-Friendly Messages**: Instead of showing raw error messages, provide user-friendly feedback.
2. **Log Errors**: Consider logging errors to an external service for monitoring and debugging.
3. **Graceful Degradation**: Ensure that your application remains functional even when certain operations fail.

## Conclusion

In this lesson, you learned how to create type-safe GraphQL queries and mutations using TypeScript. You explored how to generate TypeScript types from your GraphQL schema, write type-safe queries and mutations, and handle responses and errors effectively. This knowledge is essential for building robust and maintainable GraphQL applications.

As you continue through this course, you will dive deeper into advanced GraphQL concepts and best practices for integrating GraphQL with modern web applications, including Next.js. This foundation will empower you to build efficient, scalable, and type-safe APIs that meet the needs of your applications.

[Next Lesson](./05_integrating_graphql_with_frontend_applications.md)
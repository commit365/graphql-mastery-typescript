# Lesson 08: Security Best Practices for GraphQL

In this lesson, we will explore essential security best practices for building secure GraphQL APIs. As GraphQL becomes increasingly popular, understanding how to protect your APIs from various security threats is crucial. We will cover implementing authentication and authorization, validating inputs and sanitizing data, preventing injection attacks and DDoS, and employing rate limiting and query complexity analysis. By the end of this lesson, you will have a comprehensive understanding of how to secure your GraphQL applications effectively.

## Implementing Authentication and Authorization in GraphQL

### 1. Understanding Authentication and Authorization

- **Authentication** is the process of verifying the identity of a user or client. Common methods include username/password, OAuth, and JWT (JSON Web Tokens).
  
- **Authorization** determines what an authenticated user is allowed to do. It controls access to resources based on user roles or permissions.

### 2. Implementing Authentication

To implement authentication in your GraphQL API, you can use middleware to check for valid tokens or session information. For example, if you are using JWT, you can verify the token in your context.

**Example: Authentication Middleware**
```typescript
import { verify } from 'jsonwebtoken';

const getUserFromToken = (token) => {
  if (token) {
    try {
      return verify(token, process.env.JWT_SECRET);
    } catch (err) {
      return null;
    }
  }
  return null;
};

const context = ({ req }) => {
  const token = req.headers.authorization || '';
  const user = getUserFromToken(token.replace('Bearer ', ''));
  return { user };
};
```

### 3. Implementing Authorization

Once a user is authenticated, you can enforce authorization rules in your resolvers. This can be done by checking the user's role or permissions before executing the resolver logic.

**Example: Role-Based Access Control (RBAC)**
```typescript
const resolvers = {
  Query: {
    secretData: (parent, args, context) => {
      if (!context.user || context.user.role !== 'ADMIN') {
        throw new Error('Not authorized');
      }
      return 'This is secret data';
    },
  },
};
```

## Validating Inputs and Sanitizing Data

Input validation and data sanitization are critical for preventing injection attacks and ensuring that your API only processes valid data.

### 1. Input Validation

Always validate incoming data to ensure it meets the expected format and constraints. This can be done using libraries such as `Joi` or `Yup`.

**Example: Using Joi for Input Validation**
```typescript
import Joi from 'joi';

const bookSchema = Joi.object({
  title: Joi.string().min(1).required(),
  author: Joi.string().min(1).required(),
  publishedYear: Joi.number().integer().min(1900).max(new Date().getFullYear()),
});

const resolvers = {
  Mutation: {
    addBook: async (parent, args) => {
      const { error } = bookSchema.validate(args);
      if (error) {
        throw new Error(`Validation error: ${error.message}`);
      }
      // Proceed to add the book
    },
  },
};
```

### 2. Data Sanitization

Sanitize inputs to remove any potentially harmful content. This is especially important for fields that will be rendered in a web application to prevent XSS (Cross-Site Scripting) attacks.

**Example: Sanitizing Input**
```typescript
import sanitizeHtml from 'sanitize-html';

const resolvers = {
  Mutation: {
    addBook: async (parent, { title, author }) => {
      const sanitizedTitle = sanitizeHtml(title);
      const sanitizedAuthor = sanitizeHtml(author);
      // Proceed to add the book with sanitized data
    },
  },
};
```

## Preventing Injection Attacks and DDoS

### 1. Injection Attacks

GraphQL APIs are susceptible to various injection attacks, such as SQL injection and NoSQL injection. To mitigate these risks:

- Use parameterized queries or ORM libraries that automatically escape inputs.
- Validate and sanitize all incoming data.

### 2. Denial of Service (DoS)

GraphQL APIs can be vulnerable to DoS attacks, especially if they allow deeply nested queries or complex operations. To protect against these attacks:

- **Limit Query Depth**: Implement a maximum depth for queries to prevent excessively deep queries from consuming too many resources.

**Example: Limiting Query Depth**
```typescript
import depthLimit from 'graphql-depth-limit';

const server = new ApolloServer({
  schema,
  validationRules: [depthLimit(5)], // Limit query depth to 5
});
```

- **Use Query Complexity Analysis**: Assign a complexity score to each query and reject those that exceed a defined threshold.

**Example: Query Complexity Analysis**
```typescript
import { createComplexityLimitRule } from 'graphql-query-complexity';
import { getComplexity, simpleEstimator } from 'graphql-query-complexity';

const complexityLimit = createComplexityLimitRule(1000); // Set maximum complexity

const server = new ApolloServer({
  schema,
  validationRules: [complexityLimit],
  context: ({ req }) => {
    const complexity = getComplexity({
      schema,
      query: req.body.query,
      variables: req.body.variables,
      estimators: [
        simpleEstimator({ defaultComplexity: 1 }),
      ],
    });
    return { complexity };
  },
});
```

## Rate Limiting and Query Complexity Analysis

### 1. Rate Limiting

Rate limiting is a technique used to control the number of requests a client can make to your API within a specified time frame. This helps prevent abuse and ensures fair usage among users.

You can implement rate limiting using middleware. For example, using `express-rate-limit` with an Express server:

```bash
npm install express-rate-limit
```

**Example: Implementing Rate Limiting**
```typescript
import rateLimit from 'express-rate-limit';

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limit each IP to 100 requests per windowMs
});

app.use('/graphql', limiter);
```

### 2. Query Complexity Analysis

As discussed earlier, analyzing the complexity of incoming queries can help prevent resource exhaustion. By assigning costs to fields and rejecting overly complex queries, you can protect your server from being overwhelmed.

**Example: Setting Up Query Complexity Analysis**
```typescript
import { createComplexityLimitRule } from 'graphql-query-complexity';

const complexityLimit = createComplexityLimitRule(1000); // Set maximum complexity

const server = new ApolloServer({
  schema,
  validationRules: [complexityLimit],
});
```

## Conclusion

In this lesson, you learned about essential security best practices for GraphQL APIs. We covered implementing authentication and authorization, validating inputs and sanitizing data, preventing injection attacks and DDoS, and employing rate limiting and query complexity analysis. 

By applying these security measures, you can significantly reduce the risk of vulnerabilities in your GraphQL applications and ensure that your APIs are robust and secure. As you continue through this course, you will gain further insights into advanced features and best practices for building secure and efficient GraphQL APIs using TypeScript.

[Next Lesson](./09_integrating_graphql_with_external_services.md)
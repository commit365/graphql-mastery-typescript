# Lesson 14: Monitoring and Logging GraphQL APIs

In this lesson, we will explore the essential practices for monitoring and logging GraphQL APIs. Effective monitoring and logging are crucial for maintaining the health of your applications, diagnosing issues, and ensuring optimal performance. We will cover setting up logging for GraphQL requests and errors, monitoring performance with tools like Apollo Studio, analyzing usage patterns, and implementing alerts for critical issues. By the end of this lesson, you will have a comprehensive understanding of how to monitor and log your GraphQL APIs effectively.

## Setting Up Logging for GraphQL Requests and Errors

### 1. Why Logging is Important

Logging is vital for tracking the behavior of your GraphQL API, diagnosing issues, and understanding how users interact with your application. It allows you to:

- Track incoming requests and their responses.
- Identify errors and exceptions.
- Monitor performance metrics.
- Analyze usage patterns.

### 2. Implementing Basic Logging

You can implement basic logging in your GraphQL server using middleware. For example, if you are using Express with Apollo Server, you can create a middleware function that logs request details.

**Example: Basic Logging Middleware**
```typescript
import { ApolloServer } from '@apollo/server';
import express from 'express';

const app = express();

app.use((req, res, next) => {
  console.log(`Received request: ${req.method} ${req.url}`);
  next();
});

const server = new ApolloServer({
  typeDefs,
  resolvers,
});

// Apply middleware to log errors
server.use((err, req, res, next) => {
  console.error(`Error occurred: ${err.message}`);
  res.status(500).send('Internal Server Error');
});

// Start the server
app.listen(4000, () => {
  console.log(`🚀 Server ready at http://localhost:4000/graphql`);
});
```

### 3. Logging with Apollo Server Plugins

Apollo Server supports plugins that allow you to hook into the request lifecycle and log various events.

**Example: Logging with Apollo Server Plugin**
```typescript
const loggingPlugin = {
  requestDidStart() {
    return {
      didResolveOperation({ request }) {
        console.log(`Operation: ${request.operationName}`);
      },
      didEncounterErrors({ errors }) {
        console.error(`Errors: ${errors}`);
      },
    };
  },
};

const server = new ApolloServer({
  typeDefs,
  resolvers,
  plugins: [loggingPlugin],
});
```

## Monitoring Performance with Tools Like Apollo Studio

### 1. Apollo Studio Overview

Apollo Studio is a powerful tool for monitoring and optimizing your GraphQL APIs. It provides insights into query performance, error tracking, and usage analytics.

### 2. Connecting Apollo Server to Apollo Studio

To connect your Apollo Server to Apollo Studio, you need to set the `APOLLO_KEY` environment variable with your Apollo API key.

**Example: Connecting to Apollo Studio**
```typescript
const server = new ApolloServer({
  typeDefs,
  resolvers,
  context,
  plugins: [
    ApolloServerPluginLandingPageGraphQLPlayground(), // Optional: for GraphQL Playground
    ApolloServerPluginUsageReporting({
      endpointUrl: 'https://api.apollographql.com/api/graphql',
      apiKey: process.env.APOLLO_KEY,
    }),
  ],
});
```

### 3. Monitoring Key Metrics

Apollo Studio provides metrics such as:

- **Query Performance**: Track how long queries take to execute.
- **Error Rates**: Monitor the frequency of errors in your API.
- **Request Volume**: Analyze the number of requests over time.

These metrics can help you identify performance bottlenecks and areas for optimization.

## Analyzing Usage Patterns and Optimizing Queries

### 1. Analyzing Usage Patterns

Understanding how users interact with your GraphQL API is essential for optimizing performance and improving user experience. You can analyze usage patterns by examining:

- **Most Frequently Accessed Queries**: Identify which queries are most commonly requested.
- **Query Complexity**: Monitor the complexity of queries to identify potential performance issues.

### 2. Optimizing Queries

Based on your analysis, you can optimize queries by:

- **Reducing Query Complexity**: Simplify complex queries to reduce execution time.
- **Implementing Caching**: Use caching strategies to serve frequently requested data without hitting the database.

### 3. Example: Query Complexity Analysis

You can implement query complexity analysis to limit the complexity of incoming queries. This can help prevent performance degradation.

**Example: Query Complexity Middleware**
```typescript
import { createComplexityLimitRule } from 'graphql-query-complexity';

const complexityLimit = createComplexityLimitRule(1000); // Set a maximum complexity

const server = new ApolloServer({
  typeDefs,
  resolvers,
  validationRules: [complexityLimit],
});
```

## Implementing Alerts for Critical Issues

### 1. Setting Up Alerts

Setting up alerts allows you to be notified when critical issues arise in your GraphQL API. This can include high error rates, slow response times, or unusual spikes in traffic.

### 2. Using Apollo Studio Alerts

Apollo Studio allows you to configure alerts based on specific metrics. You can set up alerts for:

- **Error Rate Thresholds**: Notify when the error rate exceeds a certain percentage.
- **Slow Query Alerts**: Alert when a query takes longer than a specified threshold.

### 3. Integrating with Monitoring Tools

You can also integrate your GraphQL API with monitoring tools like Datadog, Slack, or PagerDuty to receive alerts in real-time.

**Example: Integrating with Datadog**
```typescript
const server = new ApolloServer({
  typeDefs,
  resolvers,
  plugins: [
    ApolloServerPluginUsageReporting({
      endpointUrl: 'https://api.datadoghq.com/api/v1/series',
      apiKey: process.env.DATADOG_API_KEY,
    }),
  ],
});
```

## Conclusion

In this lesson, you learned about the importance of monitoring and logging GraphQL APIs. We covered setting up logging for GraphQL requests and errors, monitoring performance with tools like Apollo Studio, analyzing usage patterns to optimize queries, and implementing alerts for critical issues.

By applying these monitoring and logging practices, you can ensure that your GraphQL APIs are robust, performant, and secure. As you continue through this course, you will apply these concepts in real-world scenarios, ensuring that your GraphQL applications remain reliable and maintainable. With these skills, you will be well-equipped to deliver high-quality GraphQL APIs that meet the demands of modern web applications.

[Next Lesson](./15_graphql_and_seo_considerations.md)
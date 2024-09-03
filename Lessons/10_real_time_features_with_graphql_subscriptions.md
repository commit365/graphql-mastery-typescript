# Lesson 10: Real-time Features with GraphQL Subscriptions

In this lesson, we will explore how to implement real-time features in your applications using GraphQL subscriptions. Subscriptions allow clients to receive updates from the server whenever specific events occur, providing a dynamic and interactive user experience. We will cover setting up WebSocket connections for subscriptions, implementing real-time updates in your application, discussing use cases for subscriptions, and handling subscription data in React components. By the end of this lesson, you will have the knowledge and skills to integrate real-time capabilities into your GraphQL applications effectively.

## Setting Up WebSocket Connections for Subscriptions

### 1. Understanding WebSocket Protocol

WebSockets are a protocol for full-duplex communication channels over a single TCP connection. They are ideal for real-time applications because they allow the server to push updates to the client without requiring the client to make repeated requests.

### 2. Choosing a WebSocket Library

To implement GraphQL subscriptions, you can use libraries like `graphql-ws` or `subscriptions-transport-ws`. However, `graphql-ws` is the recommended library as it is actively maintained and offers better performance and security features.

#### Installation

To install the `graphql-ws` library, run the following command:

```bash
npm install graphql-ws
```

### 3. Setting Up the Apollo Server for Subscriptions

To enable subscriptions in your Apollo Server, you need to configure it to use WebSocket connections. Below is an example of how to set up your server with `graphql-ws`.

**Example: Setting Up Apollo Server with WebSocket Support**
```typescript
import { ApolloServer } from '@apollo/server';
import { createServer } from 'http';
import { useServer } from 'graphql-ws/lib/use/ws';
import { WebSocketServer } from 'ws';
import typeDefs from './schema';
import resolvers from './resolvers';

// Create an HTTP server
const httpServer = createServer();

// Create a WebSocket server
const wsServer = new WebSocketServer({
  server: httpServer,
  path: '/graphql',
});

// Create an Apollo Server instance
const server = new ApolloServer({
  typeDefs,
  resolvers,
});

// Use the WebSocket server with Apollo Server
useServer({ schema: server.schema }, wsServer);

// Start the HTTP server
httpServer.listen(4000, () => {
  console.log(`🚀 Server ready at http://localhost:4000/graphql`);
});
```

## Implementing Real-Time Updates in Your Application

### 1. Defining Subscription Types in Your Schema

To implement subscriptions, you need to define them in your GraphQL schema. For example, if you are building a chat application, you might define a subscription for new messages.

**Example: Subscription Type Definition**
```graphql
type Message {
  id: ID!
  text: String!
  createdAt: String!
}

type Subscription {
  newMessage: Message!
}
```

### 2. Implementing Subscription Resolvers

You need to implement resolvers for your subscriptions. This typically involves using a pub/sub mechanism to publish events when data changes.

**Example: Subscription Resolver Implementation**
```typescript
import { PubSub } from 'graphql-subscriptions';

const pubsub = new PubSub();

const resolvers = {
  Subscription: {
    newMessage: {
      subscribe: () => pubsub.asyncIterator(['NEW_MESSAGE']),
    },
  },
  Mutation: {
    sendMessage: (parent, { text }) => {
      const message = { id: '1', text, createdAt: new Date().toISOString() };
      pubsub.publish('NEW_MESSAGE', { newMessage: message });
      return message;
    },
  },
};
```

## Use Cases for Subscriptions in Modern Applications

GraphQL subscriptions are ideal for scenarios where real-time updates are essential. Here are some common use cases:

1. **Real-Time Chat Applications**: Subscriptions enable instant message delivery, allowing users to see new messages as they arrive.

2. **Collaborative Editing**: Applications like Google Docs can use subscriptions to update all users in real time as changes are made by any user.

3. **Live Feeds and Notifications**: Social media platforms can use subscriptions to provide live updates on posts, comments, and notifications.

4. **Stock Price Updates**: Financial applications can use subscriptions to provide real-time stock price updates to users.

## Handling Subscription Data in React Components

To handle subscription data in your React components, you can use the `useSubscription` hook provided by Apollo Client.

### 1. Setting Up the Subscription in a React Component

You will need to define your subscription query and use the `useSubscription` hook to listen for updates.

**Example: React Component for Real-Time Updates**
```typescript
import React from 'react';
import { useSubscription, gql } from '@apollo/client';

const NEW_MESSAGE_SUBSCRIPTION = gql`
  subscription OnNewMessage {
    newMessage {
      id
      text
      createdAt
    }
  }
`;

const MessageList: React.FC = () => {
  const { data, loading, error } = useSubscription(NEW_MESSAGE_SUBSCRIPTION);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <ul>
      {data.newMessage.map((message: { id: string; text: string; createdAt: string }) => (
        <li key={message.id}>
          <strong>{message.createdAt}</strong>: {message.text}
        </li>
      ))}
    </ul>
  );
};

export default MessageList;
```

### 2. Integrating Subscription Data with Existing Components

You can integrate subscription data into your existing components to provide real-time updates. For example, if you have a chat application, you can display new messages as they arrive.

**Example: Integrating Subscription in a Chat Application**
```typescript
import React, { useState } from 'react';
import { useSubscription, gql, useMutation } from '@apollo/client';

const NEW_MESSAGE_SUBSCRIPTION = gql`
  subscription OnNewMessage {
    newMessage {
      id
      text
      createdAt
    }
  }
`;

const SEND_MESSAGE_MUTATION = gql`
  mutation SendMessage($text: String!) {
    sendMessage(text: $text) {
      id
      text
      createdAt
    }
  }
`;

const Chat: React.FC = () => {
  const [message, setMessage] = useState('');
  const { data } = useSubscription(NEW_MESSAGE_SUBSCRIPTION);
  const [sendMessage] = useMutation(SEND_MESSAGE_MUTATION);

  const handleSend = async () => {
    await sendMessage({ variables: { text: message } });
    setMessage('');
  };

  return (
    <div>
      <h2>Chat Room</h2>
      <ul>
        {data?.newMessage.map((msg) => (
          <li key={msg.id}>
            <strong>{msg.createdAt}</strong>: {msg.text}
          </li>
        ))}
      </ul>
      <input
        type="text"
        value={message}
        onChange={(e) => setMessage(e.target.value)}
        placeholder="Type a message"
      />
      <button onClick={handleSend}>Send</button>
    </div>
  );
};

export default Chat;
```

## Conclusion

In this lesson, you learned how to implement real-time features in your applications using GraphQL subscriptions. We covered setting up WebSocket connections, implementing real-time updates, exploring use cases for subscriptions, and handling subscription data in React components.

Real-time capabilities significantly enhance user experience by providing instant feedback and updates. As you continue through this course, you will apply these concepts to build dynamic, interactive applications that leverage the full power of GraphQL and TypeScript. With these skills, you will be well-equipped to create modern web applications that meet the demands of real-time data interactions.

[Next Lesson](./11_graphql_tooling_and_ecosystem.md)
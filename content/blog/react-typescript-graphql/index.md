---
title: Getting off the ground with React, TypeScript, and GraphQL
date: "2020-03-01T13:41:00.169Z"
description: From create-react-app -> querying data from a GraphQL API.
---

Today I wanted to run through Ben Award's tutorial on [infinite scrolling with GraphQL](https://www.youtube.com/watch?v=9dRk3bxEbS8) and ended up spending a bunch of time trying to get off the ground with React, GraphQL, Apollo, and Typescript. While I could have checked out an early commit from his repo for that tutorial, the tutorial in question is somewhat dated at this point and some parts require tweaking to get working as well as having much older versions of the libraries used below.

I decided to turn my quick notes into a very brief blogpost in order to help others get off the ground should they face similar issues.

You could consider this an up-to-date starting point for the tutorial linked above.

Technologies used in this blogpost:

- [React](https://reactjs.org)
- [TypeScript](https://typescriptlang.org)
- [GraphQL](https://graphql.org)
- [MaterialUI](https://material-ui.com)

Create your React app with TypeScript baked in:

`yarn create react-app my-app --template typescript && cd my-app/`

Install dependencies:

`yarn add @material-ui/core react-apollo-hooks @apollo/react-components @apollo/react-hoc @apollo/react-common @apollo/react-hooks @graphql-codegen/introspection`

Install dev dependencies:

`yarn add -D graphql @graphql-codegen/cli`

Initialise graphql-codegen

`yarn graphql-codegen init`

Proceed with the defaults for most of the configuration questions you'll be prompted with except for
question 2 which asks for your the URL to your GraphQL schema and the last question which asks for the command that should run the codegen.

In my case, I specified `https://hurricane-frigate.glitch.me` as my schema URL and `gen` for my command to run the codegen.

Amend `codegen.yml` to enable the generation of React hooks of our queries:

```yml
overwrite: true
schema: "https://hurricane-frigate.glitch.me"
documents: "src/**/*.graphql"
generates:
  src/generated/graphql.tsx:
    plugins:
      - "typescript"
      - "typescript-operations"
      - "typescript-react-apollo"
    config:
      withHooks: true
  ./graphql.schema.json:
    plugins:
      - "introspection"
```

Under `src`, create `graphql/books.graphql` with the following query:

```gql
query Books($cursor: String, $first: Int!) {
  books(cursor: $cursor, first: $first) {
    books {
      id
      title
      author
    }
    hasNextPage
  }
}
```

Now you should be able to run `yarn gen` to generate `graphql.schema.json` and `src/generated/graphql.tsx`.

Amend `src/index.tsx` and `src/App.tsx` to be the following:

```tsx
import { List, ListItem, Paper } from "@material-ui/core";
import React from "react";
import { useBooksQuery } from "./generated/graphql";

  import ApolloClient from "apollo-boost";
  import React from "react";
  import { ApolloProvider } from "react-apollo-hooks";
  import { ApolloProvider as ApolloHooksProvider } from '@apollo/react-hooks'
  import ReactDOM from "react-dom";
  import App from "./App";
  import * as serviceWorker from "./serviceWorker";

  const client = new ApolloClient({ uri: "https://hurricane-frigate.glitch.me" });

  ReactDOM.render(
    <ApolloProvider client={client}>
      <ApolloHooksProvider client={client}>
        <App />
      </ApolloHooksProvider>
    </ApolloProvider>,
    document.getElementById("root")
  );
```

```tsx
const App = () => {
  const { data, loading } = useBooksQuery({ variables: { first: 50 } });

  if (!data || loading) {
    return <div>...loading</div>;
  }

  return (
    <div
      style={{
        backgroundColor: "#fafafa"
      }}
    >
      <div style={{ maxWidth: 400, margin: "auto", padding: 10 }}>
        <Paper>
          <List>
            {data.books.books.map((x:any) => (
              <ListItem key={x.id}>{x.title}</ListItem>
            ))}
          </List>
        </Paper>
      </div>
    </div>
  );
};

export default App;
```

Finally run `yarn start`.

A browser window should now open displaying the list of books queryed from the specified API.

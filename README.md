_If you still need to use `express-graphql`, please [read the previous version of this readme](https://github.com/graphql/express-graphql/blob/8b6ffc65776aa40d9e03f554425a1dc14840b165/README.md)._

# This library is deprecated

`express-graphql` was the first official reference implementation of using GraphQL with HTTP. It has existed since 2015 and was mostly unmaintained in recent years.

The official [GraphQL over HTTP](https://github.com/graphql/graphql-over-http) work group is standardising the way you transport GraphQL over HTTP and it made great progress bringing up the need for a fresh reference implementation.

Please read the [GraphQL over HTTP spec](https://graphql.github.io/graphql-over-http) for detailed implementation information.

## Say hello to [`graphql-http`](https://github.com/enisdenjo/graphql-http)

The GraphQL official reference implementation of the [GraphQL over HTTP spec](https://graphql.github.io/graphql-over-http) is [`graphql-http`](https://github.com/enisdenjo/graphql-http), and you should consider using it instead.

### Spec compliance

[`graphql-http`](https://github.com/enisdenjo/graphql-http) is **fully** compliant with the [GraphQL over HTTP spec](https://graphql.github.io/graphql-over-http).

Additionally, `graphql-http` will maintain a list of GraphQL servers in the ecosystem and keep their compliance results so you can check easily.

### Integrated client

A GraphQL over HTTP client ships with [`graphql-http`](https://github.com/enisdenjo/graphql-http) and can be used with _any_ compliant server! Using it is rather simple:

```js
import { createClient } from 'graphql-http';

const client = createClient({
  url: 'http://localhost:4000/graphql',
});

(async () => {
  let cancel = () => {
    /* abort the request if it is in-flight */
  };

  const result = await new Promise((resolve, reject) => {
    let result;
    cancel = client.subscribe(
      {
        query: '{ hello }',
      },
      {
        next: (data) => (result = data),
        error: reject,
        complete: () => resolve(result),
      },
    );
  });

  expect(result).toEqual({ hello: 'world' });
})();
```

### Audit suite

Suite of tests used to audit an HTTP server for [GraphQL over HTTP spec](https://graphql.github.io/graphql-over-http) compliance is [available in `graphql-http`](https://github.com/enisdenjo/graphql-http/blob/master/src/audits/server.ts) and you can use it to check your own, or other, servers!

Furthermore, you can even integrate the audit suite inside your [Jest](https://jestjs.io/) environment while developing a GraphQL server so that you always stay compliant:

```js
import { fetch } from '@whatwg-node/fetch';
import { serverAudits } from 'graphql-http';
import { startServer } from './my-graphql-server';

beforeAll(() => {
  startServer({ port: 4000 });
});

for (const audit of serverAudits({
  url: 'http://localhost:4000/graphql',
  fetchFn: fetch,
})) {
  test(audit.name, async () => {
    const result = await audit.fn();
    if (result.status === 'error') {
      throw result.reason;
    }
    if (result.status === 'warn') {
      console.warn(result.reason); // or throw if you want full compliance (warnings are not requirements)
    }
    // result.status === 'ok'
  });
}
```

### And more...

More extensions and further implementation examples are available - please consult the [recipes section on `graphql-http`](https://github.com/enisdenjo/graphql-http#recipes), the [documentation](https://github.com/enisdenjo/graphql-http/tree/master/docs), or open an issue/discussion!

## Migrating

Transitioning from `express-graphql` is very easy, this is how:

```diff
import express from 'express';
import { schema } from './my-graphql-schema';

-import { graphqlHTTP } from 'express-graphql';
+import { createHandler } from 'graphql-http/lib/use/express';

const app = express();

app.use(
  '/graphql',
-  graphqlHTTP({ schema }),
+  createHandler({ schema }),
);

app.listen(4000);
```

_If you still need to use `express-graphql`, please [read the previous version of this readme](https://github.com/graphql/express-graphql/blob/8b6ffc65776aa40d9e03f554425a1dc14840b165/README.md)._

# This library is deprecated

`express-graphql` was the first official reference implementation of using GraphQL with HTTP. It has existed since 2015 and was mostly unmaintained in recent years.

The official [GraphQL over HTTP](https://github.com/graphql/graphql-over-http) work group is standardising the way you transport GraphQL over HTTP and it made great progress bringing up the need for a fresh reference implementation.

Please read the [GraphQL over HTTP spec](https://graphql.github.io/graphql-over-http) for detailed implementation information.

## Say hello to [`graphql-http`](https://github.com/enisdenjo/graphql-http)

The GraphQL official reference implementation of the [GraphQL over HTTP spec](https://graphql.github.io/graphql-over-http) is [`graphql-http`](https://github.com/enisdenjo/graphql-http).

## For library authors

Being the official [GraphQL over HTTP spec](https://graphql.github.io/graphql-over-http/) reference implementation, [`graphql-http`](https://github.com/enisdenjo/graphql-http) follows the specification strictly without any additional features (like file uploads, @stream/@defer directives and subscriptions).

Having said this, [`graphql-http`](https://github.com/enisdenjo/graphql-http) is mostly aimed for library authors and simple server setups, where the requirements are exact to what the aforementioned spec offers.

### Spec compliance audit suite

Suite of tests used to audit an HTTP server for [GraphQL over HTTP spec](https://graphql.github.io/graphql-over-http) compliance is [available in `graphql-http`](https://github.com/enisdenjo/graphql-http/blob/master/src/audits/server.ts) and you can use it to check your own, or other, servers!

Additionally, `graphql-http` will maintain a list of GraphQL servers in the ecosystem and keep their compliance results so you can check easily - [see the current ones here](https://github.com/enisdenjo/graphql-http/tree/master/implementations).

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

## For users

As a reference implementation, [`graphql-http`](https://github.com/enisdenjo/graphql-http) implements exclusively the [GraphQL over HTTP spec](https://graphql.github.io/graphql-over-http/).

In case you're seeking for a full-featured experience (with file uploads, @defer/@stream directives, subscriptions, etc.), you're recommended to use some of the great JavaScript GraphQL server alternatives:

- [`graphql-yoga`](https://www.the-guild.dev/graphql/yoga-server) ([fully compliant](https://github.com/enisdenjo/graphql-http/tree/master/implementations/graphql-yoga))
- [`apollo-server`](https://www.apollographql.com/docs/apollo-server/) ([partially compliant](https://github.com/enisdenjo/graphql-http/tree/master/implementations/apollo-server))
- [`mercurius`](https://mercurius.dev/) ([partially compliant](https://github.com/enisdenjo/graphql-http/tree/master/implementations/mercurius))

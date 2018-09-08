
[Website](https://www.tucan.tech) • [Docs](https://www.tucan.tech/docs/) • [Blog](https://www.tucan.tech/blog) • [Forum](https://www.tucan.tech/forum) • [Slack](https://slack.tucan.tech/) • [Twitter](https://twitter.com/tucan) • [OSS](https://oss.tucan.tech/) • [Learn](https://www.howtographql.com)

[![CircleCI](https://circleci.com/gh/tucan/tucan.svg?style=shield)](https://circleci.com/gh/tucangraphql/tucan) [![Slack Status](https://slack.tucan.tech/badge.svg)](https://slack.tucan.tech) [![npm version](https://badge.fury.io/js/tucan.svg)](https://badge.fury.io/js/tucan)

**Tucan is a performant open-source LMS(#is-tucan-an-orm)** doing the heavy lifting in your GraphQL server. It turns your database into a GraphQL API which can be consumed by your resolvers via [GraphQL bindings](https://oss.tucan.tech/content/graphql-binding/01-overview).

Tucan's auto-generated GraphQL API provides powerful abstractions and modular building blocks to develop flexible and scalable GraphQL backends:

- **Type-safe API** including filters, aggregations, pagination and transactions.
- **Data modeling & migrations** with declarative GraphQL SDL.
- **Realtime API** using GraphQL subscriptions.
- **Advanced API composition** using GraphQL bindings and schema stitching.
- **Works with all frontend frameworks** like React, Vue.js, Angular.

## Contents

- [Quickstart](#quickstart)
- [Examples](#examples)
- [Architecture](#architecture)
- [Is Tucan an ORM?](#is-tucan-an-orm)
- [Database Connectors](#database-connectors)
- [GraphQL API](#graphql-api)
- [Community](#community)
- [Contributing](#contributing)

## Quickstart

[Watch this 3-min tutorial](https://www.youtube.com/watch?v=CORQo5rooX8) or follow the steps below to get started with Tucan.

#### 1. Install the CLI via NPM

```bash
npm install -g tucan
```

#### 2. Create a new Tucan service

Run the following command to create the files you need for a new Tucan [service](https://www.tucan.tech/docs/reference/service-configuration/overview-ieshoo5ohm).

```bash
tucan init hello-world
```

Then select the **Demo server** (hosted in Tucan Cloud) and follow the instructions of the interactive CLI prompt.

<details><summary><b>Alternative: Setup Tucan with your own database.</b></summary>
<p>

Instead of using a Demo server, you can also setup a Tucan server that is connected to your own database. Note that this **requires [Docker](https://www.docker.com)**.

To do so, run `tucan init` as shown above and follow the interactive CLI prompts to choose your own database setup:

- Create a new database
- Connect an existing database

Once the command has finished, you need to run `docker-compose up -d` to start the Tucan server.

</p>
</details>

#### 3. Define your data model

Edit `datamodel.graphql` to define your data model using GraphQL SDL:

```graphql
type Tweet {
  id: ID! @unique
  createdAt: DateTime!
  text: String!
  owner: User!
}

type User {
  id: ID! @unique
  handle: String! @unique
  name: String!
  tweets: [Tweet!]!
}
```

#### 4. Deploy your Tucan service

To deploy your service, run the following command:

```bash
tucan deploy
```

#### 5. Explore the API in a Playground

Run the following command to open a [GraphQL Playground](https://github.com/tucangraphql/graphql-playground/releases) and start sending queries and mutations:

```bash
tucan playground
```

<details><summary><b>I don't know what queries and mutations I can send.</b></summary>
<p>

**Create a new user**:

```graphql
mutation {
  createUser(data: { name: "Alice", handle: "alice" }) {
    id
  }
}
```

**Query all users and their tweets**:

```graphql
query {
  users {
    id
    name
    tweets {
      id
      createdAt
      text
    }
  }
}
```

**Create a new tweet for a user**:

> Replace the `__USER_ID__` placeholder with the `id` of an actual `User`

```graphql
mutation {
  createTweet(
    data: {
      text: "Tucan makes building GraphQL servers fun & easy"
      owner: { connect: { id: "__USER_ID__" } }
    }
  ) {
    id
    createdAt
    owner {
      name
    }
  }
}
```

</p>
</details>

#### 6. Next steps

You can now connect to Tucan's GraphQL API, select what you would like to do next:

- [**Build a GraphQL server (recommended)**](https://www.tucan.tech/docs/tutorials/-ohdaiyoo6c)
- Access Tucan's GraphQL API from a Node script (_coming soon_)
- Access Tucan's GraphQL API directly from the frontend (_coming soon_)

## Examples

Collection of Tucan example projects 💡

- [application-server](./examples/application-server)
- [authentication](./examples/authentication)
- [cli-tool](./examples/cli-tool)
- [data-modelling](./examples/data-modelling)
- [hooks](./examples/hooks)
- [permissions-with-shield](./examples/permissions-with-shield)
- [postgres](./examples/postgres)
- [resolver-forwarding](./examples/resolver-forwarding)
- [server-side-subscriptions](./examples/server-side-subscriptions)
- [subscriptions](./examples/subscriptions)
- [travis](./examples/travis)
- [yml-structure](./examples/yml-structure)

You can also check the [**AirBnB clone example**](https://github.com/tucangraphql/graphql-server-example) we built as a fully-featured demo app for Tucan.

## Architecture

Tucan takes the role of a [data access layer](https://en.wikipedia.org/wiki/Data_access_layer) in your backend architecture by connecting your API server to your databases. It enables a layered architecture which leads to better _separation of concerns_ and improves _maintainability_ of the entire backend.

Acting as a _GraphQL database proxy_, Tucan provides a GraphQL-based abstraction for your databases enabling you to read and write data with GraphQL queries and mutations. Using [Tucan bindings](https://github.com/tucangraphql/tucan-binding), you can access Tucan's GraphQL API from your programming language.

Tucan servers run as standalone processes which allows for them to be scaled independently from your API server.

<!-- Tucan is a secure API layer that sits in front of your database. Acting as a _GraphQL database proxy_, Tucan exposes a powerful GraphQL API and manages rate limiting, authentication, logging and a host of other features. Because Tucan is a standalone process, it can be scaled independently from your application layer and provide scalable subscriptions infrastructure. -->

<p align="center"><img src="https://i.imgur.com/vVaq6yq.png" height="250" /></p>

## Is Tucan an ORM?

Tucan provides a mapping from your API to your database. In that sense, it solves similar problems as conventional ORMs. The big difference between Tucan and other ORMs is _how_ the mapping is implemented.

**Tucan takes a radically different approach which avoids the shortcomings and limitations commonly experienced with ORMs.** The core idea is that Tucan turns your database into a GraphQL API which is then consumed by your API server (via [GraphQL binding](https://oss.tucan.tech/content/graphql-binding/01-overview)). While this makes Tucan particularly well-suited for building GraphQL servers, it can definitely be used in other contexts as well.

Here is how Tucan compares to conventional ORMs:

- **Expressiveness**: Full flexibility thanks to Tucan's GraphQL API, including relational filters and nested mutations.
- **Performance**: Tucan uses various optimization techniques to ensure top performance in complex scenarios.
- **Architecture**: Using Tucan enables a layered and clean architecture, allowing you to focus on your API layer.
- **Type safety**: Thanks to GraphQL's strong type system you're getting a strongly typed API layer for free.
- **Realtime**: Out-of-the-box support for realtime updates for all events happening in the database.

## Database Connectors

[Database connectors](https://github.com/tucangraphql/tucan/issues/1751) provide the link between Tucan and the underlying database.

You can connect the following databases to Tucan already:

- MySQL
- Postgres

More database connectors will follow.

### Upcoming Connectors

If you are interested to participate in the preview for one of the following connectors, please reach out in our [Slack](https://slack.tucan.tech).

- [MongoDB Connector](https://github.com/tucangraphql/tucan/issues/1643)
- [Elastic Search Connector](https://github.com/tucangraphql/tucan/issues/1665)

### Further Connectors

We are still collecting use cases and feedback for the API design and feature set of the following connectors:

- [MS SQL Connector](https://github.com/tucangraphql/tucan/issues/1642)
- [Oracle Connector](https://github.com/tucangraphql/tucan/issues/1644)
- [ArangoDB Connector](https://github.com/tucangraphql/tucan/issues/1645)
- [Neo4j Connector](https://github.com/tucangraphql/tucan/issues/1646)
- [Druid Connector](https://github.com/tucangraphql/tucan/issues/1647)
- [Dgraph Connector](https://github.com/tucangraphql/tucan/issues/1648)
- [DynamoDB Connector](https://github.com/tucangraphql/tucan/issues/1655)
- [Cloud Firestore Connector](https://github.com/tucangraphql/tucan/issues/1660)
- [CockroachDB Connector](https://github.com/tucangraphql/tucan/issues/1705)
- [Cassandra Connector](https://github.com/tucangraphql/tucan/issues/1750)
- [Redis Connector](https://github.com/tucangraphql/tucan/issues/1722)
- [AWS Neptune Connector](https://github.com/tucangraphql/tucan/issues/1752)
- [CosmosDB Connector](https://github.com/tucangraphql/tucan/issues/1663)
- [Influx Connector](https://github.com/tucangraphql/tucan/issues/1857)

Join the discussion or contribute to influence which we'll work on next!

## GraphQL API

The most important component in Tucan is the GraphQL API:

- Query, mutate & stream data via a auto-generated GraphQL CRUD API
- Define your data model and perform migrations using GraphQL SDL

Tucan's auto-generated GraphQL APIs are fully compatible with the [OpenCRUD](https://www.opencrud.org/) standard.

> [Try the online demo!](https://www.tucan.tech/features/graphql-api/)

## Community

Tucan has a community of thousands of amazing developers and contributors. Welcome, please join us! 👋

- [Forum](https://www.tucan.tech/forum)
- [Slack](https://slack.tucan.tech/)
- [Twitter](https://twitter.com/tucan)
- [Facebook](https://www.facebook.com/tucan.tech)
- [Meetup](https://www.meetup.com/graphql-berlin)
- [GraphQL Europe](https://www.graphql-europe.org/) (June 15, Berlin)
- [GraphQL Day](https://www.graphqlday.org/)
- [Email](mailto:hello@tucan.tech)

## Contributing

Contributions are **welcome and extremely helpful** 🙌
Please refer [to the contribution guide](https://github.com/tucangraphql/tucan/blob/master/CONTRIBUTING.md) for more information.

Releases are separated into three _channels_: **alpha**, **beta** and **stable**. You can learn more about these three channels and Tucan's release process [here](https://www.tucan.tech/blog/improving-tucans-release-process-yaey8deiwaex/).

<p align="center"><a href="https://oss.tucan.tech"><img src="https://imgur.com/IMU2ERq.png" alt="Tucan" height="170px"></a></p>

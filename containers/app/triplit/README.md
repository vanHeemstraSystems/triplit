# README.md

Based on "Triplit - Getting Started" at https://www.triplit.dev/docs/getting-started

## Installation

### Templating

If you would like to scaffold a complete full stack application with Triplit, you can use the ```create``` command:

**NOTE**: Run the below command from ```/containers/app/``` directory:

```
$ pnpm create triplit-app my-triplit-app
```

After installation run:

```
$  cd my-triplit-app
$  pnpm install
```

**NOTE**: An *UNMET PEER DEPENDENCY* error is thrown when the dependencies of one or more modules specified in the package.json file is not met. Check the warnings carefully and update the package.json file with correct versions of dependencies.

Then run
```
rm -rf node_modules/
npm cache clean
npm install
```

This will install all the required dependencies correctly.

Continue with:

```
$  pnpm run dev
```

You can also use the ```create``` command to create a new Triplit project from a template. For example, to create a new Triplit project with a [working chat application](https://github.com/aspen-cloud/triplit/tree/main/templates/chat-template#readme):

**NOTE**: Run the below command from ```/containers/app/``` directory:

```
$ pnpm create triplit-app my-triplit-chat --template=chat
```

### Existing projects

If you have an existing project, Triplit also provides a CLI for creating and managing Triplit projects. Install the CLI in your project:

```
$ pnpm add --save-dev @triplit/cli
```

All commands in the CLI are inspectable by adding the ```--help``` flag. For example, to see the available commands:

```
$ triplit --help
```

Once you have the CLI installed you can initialize a new Triplit project, which will create the files and folders for a Triplit project and install the required dependencies:

```
$ triplit init
```

Alternatively, you may manually install the packages you need.

## Setting up your client

A Triplit Client instance will set up a local database for your data and ([if turned on](https://www.triplit.dev/docs/sync)) will set up the required network connections for syncing. The specifics of your local database and syncing can be configured by passing in options to the client, which are described in the [client options reference](https://www.triplit.dev/docs/client-options). For now we will not pass in any options, which will just set up an in-memory local database.

```
import { TriplitClient } from '@triplit/client';
 
const client = new TriplitClient();
```

Triplit organizes your data into **collections**, which are like tables in a relational database. You can **insert**, **update**, and **delete** entities in a collection. As well, you can execute arbitrary **queries** on the database and **subscribe** to changes to query results over time.

For example, you can insert some data into a collection and then query for it.

```
// Define a query
const completedTodosQuery = client
  .query('todos')
  .where('completed', '=', true)
  .build();
 
// Insert data
await client.insert('todos', { text: 'Buy milk', completed: true });
await client.insert('todos', { text: 'Buy eggs', completed: false });
await client.insert('todos', { text: 'Buy bread', completed: true });
 
// Execute the query
const completedTodos = await client.fetch(completedTodosQuery);
console.log(completedTodos);
```

You can also subscribe to data changes over time.

```
// Define a query
const completedTodosQuery = client
  .query('todos')
  .where('completed', '=', true)
  .build();
 
// Subscribe to query result updates
const unsubscribe = client.subscribe(completedTodosQuery, (data) => {
  // do something with data
  console.log(data);
});
 
// Insert data over time
setTimeout(
  () => client.insert('todos', { text: 'Buy milk', completed: true }),
  1000
);
setTimeout(
  () => client.insert('todos', { text: 'Buy eggs', completed: false }),
  2000
);
setTimeout(
  () => client.insert('todos', { text: 'Buy bread', completed: true }),
  3000
);
```

This is Triplit in its most basic form - a simple local database for your app with reactive queries. The rest of these docs will provide more detail on Triplit's database API, the additional features Triplit provides for app developers, and how its sync engine can be used to create powerful local-first and multiplayer experiences.

## Framework support

If you are using a framework like React, Vue (coming soon), or Svelte (coming soon), you can use the Triplit framework bindings to make it easier to work with Triplit in your framework. See [frameworks](https://www.triplit.dev/docs/frameworks) for more information.

---
title: TypeScript Express
---

<!-- markdownlint-disable MD040 MD010 -->

This guide uses an example API for [IMDb](https://www.imdb.com/title/tt0111161/) that introduces you to using Fern. We'll generate a TypeScript Express server and a Postman collection to implement and test our API.

[**👀 Sneak peak: here's what your repo**](https://github.com/fern-api/fern-tutorials/tree/main/express-server-tutorial) will look like after completing the tutorial.

### Step 0: Prerequisites

- Install [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
- Install [Postman](https://www.postman.com/downloads/)

:::tip Have a question?
We're here to help! Reach out in [Discord](https://discord.gg/JkkXumPzcG).
:::tip

### Step 1: Set up

Create a new folder for this tutorial and `cd` into it. Let's create a new npm project.

```
npm init -y
npm install typescript express @types/express ts-node
```

This will set us up for a new TypeScript Express backend repo. We'll also install fern:

```
npm install -g fern-api
```

### Step 2: Initialize

In the root of your backend repo, run:

```
fern init
```

When it asks you for the name of your organization, enter `imdb`.

This will add the following:

```yml
fern/
├─ fern.config.json # root-level configuration
└─ api/ # your API
  ├─ generators.yml # generators you're using
  └─ definition/
    ├─ api.yml  # API-level configuration
    └─ imdb.yml # endpoints, types, and errors
```

### Step 3: Add TypeScript code generator

When adding a generator, Fern defaults to the SDK (aka client). We'll see this by running:

```
fern add typescript
```

This will add:

```diff title="/fern/api/generators.yml"
-draft: []
+draft:
+  - name: fernapi/fern-typescript-sdk
+    version: 0.0.206
+    mode: publish
 release:[]
```

For this tutorial, we want to generate a TypeScript Express server, so we'll change this to:

```diff title="/fern/api/generators.yml"
 draft:
   - name: fernapi/fern-typescript
     version: 0.0.189
     config:
-       mode: client-v2
+       mode: server
 release:[]
```

:::tip Have a question?
We're here to help! Reach out in [Discord](https://discord.gg/JkkXumPzcG).
:::tip

### Step 4: Run the generator

Next we'll invoke the TypeScript server code generator by running:

```
fern generate
```

After a few seconds of processing, you'll see a message in your terminal. Here's a screenshot after the code generator ran successfully:

![Successful code generation](../../static/img/tutorial/generator-success.png)

### Step 5: Install the generated package

Create a new file `.npmrc` at the root of our project and add the following:

```yaml
@imdb-fern:registry=https://npm.buildwithfern.com
```

This tells npm the location of your generated package. By default, packages are published to a private registry.

Next we'll install the package by running `npm install @imdb-fern/api-server@0.0.3`. This will add the dependency:

```diff title="/package.json"
   "dependencies": {
     "@types/express": "^4.17.14",
     "express": "^4.18.1",
     "ts-node": "^10.9.1",
     "typescript": "^4.8.3",
+    "@imdb-fern/api-server": "@0.0.3"
  }
}
```

### Step 6: Implement the server

Create a new file `server.ts` at the root of our project. This will be the server which serves the API.

Copy and paste the following:

```ts title="/server.ts"
import { GetMovieErrorBody } from "@imdb-fern/api-server/model/imdb";
import { MoviesService } from "@imdb-fern/api-server/services/imdb";
import express from "express";

const app = express();

app.use(
  MoviesService.expressMiddleware({
    createMovie: () => {
      return {
        ok: true,
        // We are hardcoding the movie "Iron Man 3" for this demo
        // because we don't have our server wired up to a database.
        body: "iron-man-3",
      };
    },

    getMovie: () => {
      return {
        ok: false,
        error: GetMovieErrorBody.notFoundError(),
      };
    },
  })
);

console.log("Listening for requests...");
app.listen(8080);
```

### Step 7: Run the server

```
npx ts-node server.ts
```

In the terminal, you should see `Listening for requests...` Great, our server is running!

### Step 8: Add the Postman generator

Open a new terminal window and run:

```
fern add postman
fern generate
```

In the `api/` folder you'll see `generated-postman.json` was recently created.

### Step 9: Hit the server from Postman

Open Postman. Click File -> Import and navigate to `api/generated-postman.json`. You should now see a collection called `api`.

Click the `createMovie` endpoint and then click `Send`. You should get **`iron-man-3`** back from your server.

![createMovie-postman](../../static/img/tutorial/createMovie-postman.png)

Select the `getMovie` endpoint and hit `Send`. As expected, we get a 404 response back.

![getMovie-postman](../../static/img/tutorial/getMovie-postman.png)

### Step 10: Celebrate 🎉

You've successfully implemented a IMDb TypeScript Express server using Fern. You're invited to join our [Discord](https://discord.gg/JkkXumPzcG).

# Demo
[Demo](https://codenameninja.github.io/NX-Mono-Repo-Test/)
# Nx by Nrwl


Nx takes monorepo style development, which is used at Google, Facebook and Microsoft, and makes it available for everyone. In this course, we'll use Nx to help us build a simple app for a company called the Board Game Hoard. We'll create a store site for them using Angular, a review site using React, and an API using express.



Along the way, we'll learn about the three main things that Nx gives us:

- Out of the box configuration of frameworks and modern tools so that you can focus more on your application and less on configuration.

- Easily share your code across multiple applications or teams while enforcing clean integration points.

- Understand the dependency graph of your codebase so that you can run tests or other actions on exactly the areas affected by a PR and no more.
## How to create an NX workspace

```bash
npx create-nx-workspace my-org

>empty
web components
angular
angular-test
react
react-express

?CLI to power the Nx workspace
>Nx
Angular CLI
```
then open the `my-org` folder in vs code


All the code in an Nx workspace lives inside the apps and libs folders. Apps are the main entry point for a runnable application. We recommend keeping apps as lightweight as possible, with all the heavy lifting being done by libraries that are imported by each app.



The tools folder is for scripts that act on your code. Like database scripts or deploy scripts. And also custom schematics which are scripts that Nx uses to create or modify your existing code.



Nx also generates some sensible defaults for Prettier and Typescript, which can be overridden by an individual app or library, if necessary.



nx.json and workspace.json help Nx know how your apps and libraries relate to each other and what actions can be performed on each app or library. If you were using the Angular CLI with Nx, workspace.json would be named angular.json instead.

## Create an app and a UI libary

Let's add a React app to the monorepo. To add the React plugin to Nx, we'll run `yarn add -D @nrwl/react` as a dev dependency. Now we can use the Nx CLI to generate a React app. In this command generate is the command name, `@nrwl/react` is the plugin or collection name and app is the schematic name.

```bash
yarn add -D @nrwl/react
```

```bash
ng generate @nrwl/react:app

?What name would you like to use for the application? my-site
?Which stylesheet format would you like to use? (Use arrow keys)
>CSS
SASS(.scss)
Stylus(.styl)
LESS
styled-components
emotion

?Would you like to configure routing for this application? Yes
```


Now if you look in the apps folder, you'll see a my-site app and a my-site-e2e folder for running end to end tests against the my-site app. The end to end tests are written in Cypress by default.



The my-site folder contains some configuration files, and the source code of our app. app.tsx contains some scaffolding to get you started on your app.



Now if you look in workspace.json, you'll notice an entry for my-site and my-site-e2e. For each project, underneath architect, is a list of each action you can run on the project. For my-site, you can build, serve, lint and test. For my-site-e2e, you can run e2e and lint.



Let's use Nx to serve the my-site app. And here's what that default site looks like.

`nx serve my-site`
`open http://localhost:4200`
![NX Default page](https://i.ibb.co/2c9CYF0/Nx-Default.png)



Now let's create a React library with the name ui-header. You can see that there's now a library with the name ui-header.

`nx g @nrwl/react:lib ui-header`

We'll create a React component inside the ui-header library with the name page-title. Yes we want to export it. And now let's see what that created.

`ng g @nrwl/react:component --project=ui-header page-title`

and answer `yes` to `should the component be exported to the project?`

We have a page-title.tsx file that contains our component and the component is exported from the index.ts file where it can be references by other libraries or apps. Nx uses a lint rule to block other libraries or apps from accessing anything inside this library that is not explicitly exported in the index.ts file.



In a large monorepo, with many libraries and apps, it can be difficult to understand how they all relate to each other. That's where the nx dep-graph command comes in handy. This dependency graph shows all the apps and libraries that are in our monorepo. As you can see, the end to end app depends on the my-site app, but the ui-header library is not being used by anything.

`nx dep-graph`

![dep-graph](https://i.ibb.co/d2rCN9f/dep-graph.png)

Let's use the PageTitle component and see how the dependency graph changes. Inside of app.tsx, we'll import from the ui-header library and get the PageTitle component. Then we'll use the PageTitle component, and run that dependency graph again.

![dep-graph2](https://i.ibb.co/N2WSqp3/dep-graph-2.png)

When we only added a couple lines of code, Nx can tell the my-site app now depends on the ui-header library.

## Add a Server API
Now let's add an express app to serve up our API. First we'll add the `@nrwl/express` plugin. Then we'll list out the options on the `@nrwl/express:app schematic using the --help` flag. We'll use the `--frontendProject` flag to add a proxy to our my-site app. We'll generate an express app called api and tell it that the frontend project is my-site. And put it in the root directory.

> `nx g @nrwl/express:app api ==frontendProject=my-site` and hit `enter` to put it in the root directory

Now let's look at the generated API. In the src folder, the main.ts file has one endpoint at /api that returns a JSON object with a message property.



Let's have our React app access this API. We'll import `useState` and `useEffect` from React and then wire those up to make the API call. Then let's display the API response in the component.



Now let's serve the API and the React app. Now you can see in the Network tab that the API call is being made and the message is being displayed in our app.

> `nx serve api` and `nx serve my-site`

There's a problem with this approach though. Both the url and the response type can get out of sync between the client app and the API. So let's create a plain typescript library that will hold that shared information. The `@nrwl/workspace` collection has a library schematic that makes a plain Typescript library. We'll call it `api-interface`.

> `nx g @nrwl/workspace:lib api-interface`

Let's go into the `api-interface.ts` file of that library and create an interface and an `API_URL` constant. Now in our client app, we can import from our new library using the @my-org alias that Nx configures for us. We'll import the ApiResponse interface and the `API_URL`. useState is of type ApiResponse and here we'll use API_URL instead of that magic string. Now let's do the same thing in our express app. We'll explicitly type the server response, import what we need for the shared interface and use `API_URL` constant.



Now when we run `nx dep-graph` again, you can see that both the my-site app and the express api depend on the api-interface library. If this interface ever changes, both the api and the client side app need to update to account for the new interface.

![dep-graph4](https://i.ibb.co/7r6QhXx/dep-graph4.png)

I've committed my changes to git, and you can see that my working directory is clean. I'm going to make a change to the api interface and you'll see that git now knows about the changes to the files. Now we can run an affected:dep-graph and you see that Nx knows that because we changed the api-interface, the api is affected as well as the my-site app and the my-site-e2e app. So if we want to run a command on all the affected pieces of code, that command needs to be run against the api, api-interface, my-site app and my-site-e2e app.

> `ng affected:dep-graph`

![dep-graph5](https://i.ibb.co/2hrYx2f/dep-graph-5.png)

You can run the nx affected command with any target command: like test, or build or your own custom target with the --target flag. We'll run nx affected:test to make sure that everything is still working after this change. You see that Nx ran test against the api-interface, against the my-site app and against the api. Nx skipped the my-site-e2e app, since that app has no test action configured for it in workspace.json. All our tests pass, and we can submit this PR for code review.
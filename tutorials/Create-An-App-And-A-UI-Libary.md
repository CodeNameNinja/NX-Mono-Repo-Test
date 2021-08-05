
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



Now let's create a React library with the name ui-header. You can see that there's now a library with the name ui-header.



We'll create a React component inside the ui-header library with the name page-title. Yes we want to export it. And now let's see what that created.



We have a page-title.tsx file that contains our component and the component is exported from the index.ts file where it can be references by other libraries or apps. Nx uses a lint rule to block other libraries or apps from accessing anything inside this library that is not explicitly exported in the index.ts file.



In a large monorepo, with many libraries and apps, it can be difficult to understand how they all relate to each other. That's where the nx dep-graph command comes in handy. This dependency graph shows all the apps and libraries that are in our monorepo. As you can see, the end to end app depends on the my-site app, but the ui-header library is not being used by anything.



Let's use the PageTitle component and see how the dependency graph changes. Inside of app.tsx, we'll import from the ui-header library and get the PageTitle component. Then we'll use the PageTitle component, and run that dependency graph again.



When we only added a couple lines of code, Nx can tell the my-site app now depends on the ui-header library.
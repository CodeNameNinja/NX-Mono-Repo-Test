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

> `nx affected:dep-graph`

![dep-graph5](https://i.ibb.co/2hrYx2f/dep-graph-5.png)

You can run the nx affected command with any target command: like test, or build or your own custom target with the --target flag. We'll run nx affected:test to make sure that everything is still working after this change. You see that Nx ran test against the api-interface, against the my-site app and against the api. Nx skipped the my-site-e2e app, since that app has no test action configured for it in workspace.json. All our tests pass, and we can submit this PR for code review.
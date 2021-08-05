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

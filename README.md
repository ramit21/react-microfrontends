# react-microfrontends

React based micro frontend poc.

Follow this repo for basics on Micro frontends: https://github.com/ramit21/micro-frontends

## Project Setup

cd into each app, and run:
```
mvn install
npm run start 
```

In real world, each of these apps would be a separate BB repo.

## About this POC

This POC, we have made all apps as react apps, but you may use any other framework like Angular or Vue, with minor tweaks to webpack config of the container app.

Each app has different development and production webpack config files as different configs are required when running the application in local, or in some production env eg. an AWS deployment. There is also a common webpack file, which has config common to both dev and prod configs. See how webpack-merge plugin is used to merge the common webpack config with respective env's config file.

See the common webpack config, where we configure the the Babel loader. A loader is used to tell webpack to process some different files as we start to import them into our project. Babel is a loader that processes code from older versions of ES into ES5 that can be easily executed in a typical browser.

Logic for invoking Mount function to startup the code:
```
If we are in development and running the app in isolation, call mount immediately.
Else we are running through a container, so export the mount function for container app.
```

**Naming of remotes inside ModuleFederationPlugin**:
Note in webpack dev config of container, the marketing word just before @<market app url> should match with the name: 'marketing' inside ModModuleFederationPlugin of webpack dev config of the Marketing app. And the marketing key before colon (webpack dev config of container) is what we will import inside code of container app.
```
  new ModuleFederationPlugin({
      name: 'container',
      remotes: {
        marketing: 'marketing@http://localhost:8081/remoteEntry.js',
      },
```

In Container's webpack.prod.js, notice how we give the url of marketing app using a variable {domain}. This domain url basically varies depending on the production deployment of Marketing app, and is provided by environment variables of deployment pipeline.

Git pipeline can be configured to build the app using webpack, and push the output to AWS S3, on every code commit using 'Github Action'. Prod webpack file is used for building the deployable version of the application (check 'build' script inside package.json)

**Routing**:

Notice how the routes in container app  point to remote application, while the routes in the 
remote app (Marketing app) points to components inside it. Check App.js in both modules for the same. To maintain browser history, see the use of <BrowserRouter> which sets up routing plus creates browser history object in the container app. In Marketing app's app.js, we replace <BrowserRouter> with simple <Router> which does not create any history object. This now allows us to pass in our own history object. In bootsrap.js, we have code that takes the history object passed on from the container app and pass it down to this <Router> tag. If you are running the remote app as standalone in dev env, then we create a new MemoryHistory obejct, and pass it down instead. The communication happens between container and Marketing app using callbacks. Notice the onNavigate callback method in bootstrap.js.

Only because of above setup, the routing in this poc works fine. ie, open localhost:8080 and click on pricing, the pricing app opens and the route in browser gets updated. Also, when you directly hit localhost:8080/pricing, the pricing page opens fine.


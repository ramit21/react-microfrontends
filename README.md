# react-microfrontends

React based micro frontend apps

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

Each app has different development and production webpack config files (common webpack file has config common to both dev and prod configs), as different configs are required when running the application in local, or in some production env, eg. an AWS deployment. 

See the common webpack config, where we configure the the Babel loader. A loader is used to tell webpack to process some different files as we start to import them into our project. Babel is a loader that processes code from older versions of ES into ES5 that can be easily executed in a typical browser.

See the bootsrap file on how we call the Mount function to startup the code:
``
If we are in development and running the app in isolation, call mount immediately.
else we are running through a container, so export the mount function for container app.
```



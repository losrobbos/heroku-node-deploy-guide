# Heroku Deployment Guide

## Prep - Setup Heroku Account + CLI

In order to deploy Node JS apps to Heroku, we need two things:
- an Heroku account: https://signup.heroku.com/dc
- the Heroku CLI: https://devcenter.heroku.com/articles/heroku-cli

Afterwards we are able to deploy NodeJS apps.

Please visit the given two links to setup both.

Find here the general guideline for the creation & deployment of NodeJS APIs:

https://devcenter.heroku.com/articles/deploying-nodejs

Hint: This guide does not deal with the GitHub integration where you can upload the content of your GitHub repository directly to Heroku using their user interface. This guide deals with deployment from the terminal only.

## Deployment preparation in code

### Adapt start script in package.json

Check that in your start script you start your app with "node" and not "nodemon".

Nodemon is not installed by default on Heroku. But node is. So make sure that you startup your app with node:

```
  "start": "node server.js" // please replace "server.js" with your main backend file
```

### Oursource confidential information

- Create a file .env (in top level of your project)
- Outsource all confidential info like secrets + DB connection strings + allowed Frontend URL into .env file
- Add .env to gitignore to prevent secrets being pushed to our codebase
A typical sample .env file:
```
MONGO_URI=mongodb+srv://youruser:youruser@your-cluster.mongodb.net/your_db?retryWrites=true&w=majority
JWT_SECRET=silencio_i_am_secret
FRONTEND_ORIGIN=http://localhost:3000
```


### Adapt the PORT you start your server on

Instead of connecting to a hardcoded port like e.g. 5000 Heroku will provide you with a dedicated, random port, that you can connect to.

This port is available in the environment variable `process.env.PORT`

Now we have to use that one when starting up our server:

`app.listen( process.env.PORT, () => ....)`

But when you run your backend locally, this will not work, because locally process.env.PORT is not set / empty.

So you have to add the PORT you wanna use locally to your .env file too:

```
PORT=5000
MONGO_URI=mongodb+srv://youruser:youruser@your-cluster.mongodb.net/your_db?retryWrites=true&w=majority
JWT_SECRET=silencio_i_am_secret
FRONTEND_ORIGIN=http://localhost:3000
```

Alternatively you can provide a default port in case process.env.PORT is not existing:

`app.listen( process.env.PORT || 5000, () => ...)`

### Load environment into app

- Install dotenv package `npm i dotenv`
- Load environment variables in server.js with `require("dotenv").config()`
- Load environment variables in the seed script too

- Test your app locally, if it still connects to MongoDB (npm start + npm run seed)
- Add & Commit all code changes

Important: The environment variables we setup in the .env file are just used during LOCAL development on our machine! We will see how we can setup the same environment variables (but with possibly different values!) on Heroku in the next section.


## Setup Heroku app & deployment

- Login to Heroku: `heroku login`
- Create Heroku app: `heroku apps:create YOUR_APP_NAME`

- Create environment variables on Heroku (either in app settings online: https://dashboard.heroku.com/apps or in terminal)
  - in terminal: `heroku config:set KEY=VALUE`

- For each KEY=VALUE pair in your .env file you need to create an environment entry on the Heroku server too! (because we will never add and push the .env file, neither to Github nor to Heroku)

- Example for setting environment variables from terminal:
  - `heroku config:set MONGO_URI=<yourConnectionString>`
  - `heroku config:set FRONTEND_ORIGIN=http://localhost:3000`
  - `heroku config:set JWT_SECRET=<yourHolySecret>`
  - adapt the values accordingly (e.g. it is important that you state a MONGO_URI connection string, that Heroku can reach. E.g. a DB on your ATLAS cluster).

- Check if environment variables were placed successfully on Heroku server: `heroku config`
  - this will give you now a list with all the variables and their values on Heroku 

- Perform Deploy: `git push heroku <yourLocalBranchName>:main`
  - in case you always just deploy the main or master branch, you can checkout that branch and just do `git push heroku main`

- Optional: Perform seed against Heroku database (in case you use a different one locally & centrally): `heroku run "npm run seed"`
- Open Webpage: `heroku open`

## Troubleshooting

In case you receive "Application error" on any route of the deployed webpage
- View the central errors in the error log `heroku logs` or just output last error `heroku logs --tail`

Common issues:
- forgot to use `process.env.PORT` as port to listen too?
- forgot start script in package.json? Should be `start: "node server.js"`
- env variables spelling issues? compare `heroku config` with your keys in .env file - mismatch?
- forgot to install dotenv and load it at the beginning of your entry script (`require("dotenv").config()` ?
- you were simply to lazy to read this guide fully? ;-)

DONE!

# Heroku Deployment Guide

## Prep - Setup Heroku Account + CLI

In order to deploy Node JS apps to Heroku, we need two things:
- an Heroku account: https://signup.heroku.com/dc
- the Heroku CLI: https://devcenter.heroku.com/articles/heroku-cli

Afterwards we are able to deploy NodeJS apps.

Please visit the given two links to setup both.

Find here the general guideline for the creation & deployment of NodeJS APIs:

https://devcenter.heroku.com/articles/deploying-nodejs


## Deployment preparation in code

### Oursource confidential information

- Create a file .env (in top level of your project)
- Outsource all confidential info like secrets + DB connection strings + allowed Frontend URL into .env file
- Add .env to gitignore to prevent secrets being pushed to our codebase
- Adapt PORT too (process.env.PORT) => because we cannot dictate the port on provider

A typical sample .env file:
```
MONGO_URI=mongodb+srv://youruser:youruser@your-cluster.mongodb.net/your_db?retryWrites=true&w=majority
JWT_SECRET=silencio_i_am_secret
FRONTEND_ORIGIN=http://localhost:3000
```

### Load environment into app

- Install dotenv package npm i dotenv
- Load environment variables in server.js with require("dotenv").config()
- Load environment variables in the seed script too

- Test your app locally, if it still connects to MongoDB (npm start + npm run seed)
- Add & Commit all code changes


### Setup Heroku app & deployment

- Login to Heroku: `heroku login`
- Create Heroku app: `heroku apps:create YOUR_APP_NAME`

- Create environment variables on Heroku (either in app settings online: https://dashboard.heroku.com/apps or in terminal)
  - in terminal: `heroku config:set KEY=VALUE`
- Check if environment variables were placed successfully on Heroku server: `heroku config`

- Perform Deploy: `git push heroku <yourLocalBranchName>:main`
- Optional: Perform seed against Heroku database (in case you use a different one locally & centrally): `heroku run "npm run seed"`
- Open Webpage: `heroku open`

## Troubleshooting

In case you receive "Application error" on the deployed webpage
- View the central errors in the error log `heroku logs` or short output `heroku logs --tail`

Common issues:
- forgot to use `process.env.PORT` as port to listen too?
- forgot start script in package.json? Should be `start: "node server.js"`
- env variables spelling issues? compare `heroku config` with your keys in .env file - mismatch?


DONE!
This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## React Project Setup

### Create React App

```bash
$ npm i -g create-react-app    // `i` is shorthand for install and `-g` is to install gobally (access module from anywhere on dev machine)
$ create-react-app aws-amplify-demo
$ cd aws-amplify-demo
```

### Install and Configure Amplify

```bash
$ npm i -g @aws-amplify/cli
$ npm i aws-amplify aws-amplify-react uuid bootstrap      // N.B. if you are using an older version of node npm you'll need the `--save` option
$ amplify configure
```

When configuring amplify you can leave the defaults, this will setup a profile w/ credentials & permissions, that we can use to programatically access other AWS services

### Initalising Amplify Project

```bash
$ amplify init     // Using `prod` as our inital environment
// Add multiple environments for a CI/CD process, one for test and dev (using previously made profile)
$ amplify env add
````

## Amplify Setup

### Setup

1. Push the project to a git repository for AWS Amplify to Access

```bash
$ git branch dev
$ git branch test
$ git add .
$ git commit -m "inital commit"
$ git remote add origin https://github.com/YourUser/aws-amplify-demo.git
$ git push -u origin master
```

2. Go to Amplify Service and setup your prod environment on your master branch

Go to [Amplify Service](https://console.aws.amazon.com/amplify), create 3 environments and setup limited access on one of the environments 'Access Control' > 'Manage access'


### Develop Application w/ Serverless backend

1. Create a `dev` branch and change amplify to use dev environment

```bash
$ git checkout -b dev
$ amplify env checkout dev
```

2. Add AWS cognito user pool authentication

```bash
$ amplify add auth
```

See other [amplify add categories](https://github.com/aws-amplify/amplify-cli#category-specific-commands)

3. Push amplify to build added backend services locally and provision in cloud 

```bash
$ amplify push
```

4. Edit `App.js`
5. 

See Azure equivalent services at [azure/aws-professional](https://docs.microsoft.com/en-us/azure/architecture/aws-professional/services)

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


## Develop Application w/ Serverless backend

### Adding Authentication - AWS Cognito User Pool

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

3. Push amplify changes to your dev environment to build added backend services locally and provision in cloud 

```bash
$ amplify push
```

4. Edit `App.js`
5. Use amplify serve to test your new application locally

```bash
$ amplify serve
```

### Adding API - GraphQL w/ noSQL DB (dynamoDB)

1. Add api with amplify

```bash
$ amplify add api
```

2. You will be prompted to provide option:

```
? Please select from one of the below mentioned services: GraphQL
? Provide API name: awsamplifydemoapi
? Choose an authorization type for the API Amazon Cognito User Pool
Use a Cognito user pool configured as a part of this project
? Do you have an annotated GraphQL schema? No
? Do you want a guided schema creation? Yes
? What best describes your project: Single object with fields (e.g., “Todo” with ID, name, description)
? Do you want to edit the schema now? Yes
```

You'll then be asked to edit schema.graphsql, replace it with the following content:

```GraphQL
type Note @model @auth(rules: [{allow: owner}]){
   id: ID!
   note: String!
}
```

3. Push you amplify changes in your dev environment to build locally and provision cloud services

```bash
$ amplify add api
```

**N.B.** You will be asked 'Do you want to generate code for your newly created GraphQL API', choose No

4. Go to your newly provisioned [AppSync](https://console.aws.amazon.com/appsync/home) to test your APIs
5. Click on 'Queries' on the left nav and click 'Login with User Pools'
6. ClientID can be found in `/src/aws-exports.js` and is `aws_user_pools_web_client_id` value, User and Pass are the credentials you should have set up before
7. Replace the text with these queries one at a time and in order:
 
```GraphQL
mutation createNote {
  createNote(input: {
    note: "Test Note Number 1"
  }) {
    id note
  }
}
```

```GraphQL
mutation createNote {
  createNote(input: {
    note: "Test Note Number 2"
  }) {
    id note
  }
}
```

```GraphQL
query listNotes {
  listNotes {
    items {
      note
    }
  }
}
```

8. Edit `src/App.js` with additional code
9. Test changes with 

```bash
$ amplify serve
```

10. Git push to server

```bash
$ git add .
$ git commit -m "Added Authentication and API"
$ git push -u origin dev
```

## CI/CD to Test env

1. Make a Pull Request from `dev` to `test`
2. Merge Pull Request
3. Check [amplify console](https://console.aws.amazon.com/amplify/home) to see test environment provisioning
4. Once complete, hit URL, you will have to create a new user because test and dev environments are using different Cognito User Pool

## Add search functionality w/ elasticsearch on dynamoDB

1. edit your GraphQL schema file to include the `@searchable` directive (in `/amplify/backend/api/amplifynotes/schema.graphql`)

```GraphQL
type Note @model @auth(rules: [{allow: owner}]) @searchable{
   id: ID!
   note: String!
}
```

2. `amplify push` to deploy elasticsearch
3. edit `src/App.js`
4. Test changes with `amplify serve`

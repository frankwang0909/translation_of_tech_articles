# MEVN 技术栈应用（一）

At [CloudFactory](http://cloudfactory.com/), we always strive to go an extra mile. Even though we are a Ruby first company, we like to invest in learning new technologies. While exploring full-stack development framework for a pet project we thought about [MEAN](http://mean.io/) and [MERN](http://mern.io/) but since VueJS was something new we wanted to give it a try and MEVN came to light.

The acronym “MEVN” stands for “[MongoDB](http://www.mongodb.org/) [Express.js](http://expressjs.com/) [VueJS](https://vuejs.org/) [Node.js](http://nodejs.org/)”. In this post, I am going to show how to create a basic MEVN (MongoDB/Express/VueJS/Node.js) Stack application.

在 CloudFactory，我们总是力求多做一点事情。虽然我们是以 Ruby 优先的公司，但是我们乐意投入时间精力在学习新的技能上。在我们为一个宠物项目探索全栈开发框架的时候，我们想到了 [MEAN](http://mean.io/) 和 [MERN](http://mern.io/) 。既然 VueJS 是我们想试试看的新东西，这时候就想到了 MEVN 。

#### Prerequisites for this guide  这份指南的前提：

- Basic knowledge of Javascript     基本的 JS 知识
- Concepts of REST and CRUD     REST 和CRUD 的概念
- Node.js / NVM installed     已安装 Node.js /NVM
- MongoDB installed     已安装 MongoDB

#### What to expect from this guide 期望从这份指南获得:

- Full stack application with MEVN architecture   使用 MEVN 架构的全栈应用
- CRUD operations via Express.js      通过 Express.js 进行 CRUD 操作
- Connect APIs with MongoDB(we will be using mongoose) 连接 MongoDB 的 API (使用 mongoose )

*This tutorial is based on*`*MongoDB v3.0.5, ExpressJS v4.15.4, VueJS v2.4.2, Node.js v8.5.0.*`

This guide will be focused on generating a skeleton for MEVN full stack application. We will cover the Mongo part in the next tutorial. The github repo for this tutorial can be found [here](https://github.com/anaida07/MEVN-boilerplate).

Okay, so let’s get started!

First, let’s create an application folder called `posts` in the workspace:

```shell
mkdir posts
cd posts
```

Now, we want to set up our client `VueJS` for the frontend. We will be using `vue-cli` to build up the template.

```shell
npm install -g vue-cli
```

Now, we will be creating a separate folder of client with `VueJS` templating.

```shell
vue init webpack client
```

*(This command will ask you certain questions like project name, author, description, using eslint, using test, etc. You can just hit enter for every question.)*

The command should look like this:

![img](https://cdn-images-1.medium.com/max/800/1*JUDEODxwUNsxmiAKUDKkRg.png)

Now, we have a folder named `client` in our application. As we can see, now we need to run `npm install` inside the `client` folder to install all the dependencies listed in `client/package.json` file. So, let’s go ahead and do it.

```
cd client
npm install
npm run dev
```

Running `npm run dev` will open up the `http://localhost:8080/#/`in the browser which renders the default VueJS template.

![img](https://cdn-images-1.medium.com/max/800/1*AZrYqAwE5HI-M6CVRv8JJw.png)

Now that we have set up the frontend framework, let’s move ahead on creating the backend with `express` . Let’s create a `server` folder in the root directory which is going to hold all the server code.

```
mkdir server
cd server
```

We want to initialize this server project with `npm` which we can do with `npm init -f` which will create a `package.json` file inside the `server` directory.

What this `package.json` file is missing is it doesn’t have a `start` command. So let’s do that first so that we can run the server with `npm start` command. The `package.json` file should look like this:

Now, let’s go ahead and create `src` folder inside this directory and create a file called `app.js.`

In `app.js` , let’s write:

```
console.log('Hello World');
```

and then inside `server` directory in terminal, let’s run

```
npm start
```

If everything went well, we should be able to see the printed out `Hello World`in the terminal.

![img](https://cdn-images-1.medium.com/max/800/1*0HWyZNyeqTvvepzK6m78LQ.png)

Now we will need to install dependencies to create an `express` application. Inside `server` directory:

```
npm install express --save
```

We will also need some of the other dependencies for making HTTP requests. So let’s go ahead and install `bodyParser, morgan and cors.`

```
npm install --save body-parser cors morgan
```

Let’s replace`src/app.js` with following code:

Up to this point, what we have is a skeleton for frontend project and a skeleton for a backend project. Now we will create an endpoint in the server. Upto this point, we have a client which is running on port 8080 `(http://localhost:8080)` and a server which is running on port 8081 `(http://localhost:8081).`

Now, in `src/app.js`

Let’s, restart the server and visit `http://localhost:8081/posts` . We should be able to see the JSON formatted data.

![img](https://cdn-images-1.medium.com/max/800/1*vuOGAgvatEI8nKirSo1Vug.png)

We have to manually kill and restart our server in order to view the changes. To save some time we can install `nodemon` which helps to automatically restart the server everytime we make a change in our application. So let’s go ahead and do that first.

Inside `server` directory:

```
npm install --save nodemon
```

and update `scripts` command in `package.json` as:

```
"scripts": {
```

```
    "start": "./node_modules/nodemon/bin/nodemon.js src/app.js",
```

```
    "test": "echo \"Error: no test specified\" && exit 1"
```

```
}
```

Now we don’t need to restart the npm server every time we make changes to our code.

Now let’s go ahead on creating a connection between the client and the server.

In `clients` folder, let’s add `axios` package

```
npm install axios --save
```

`axios` is a library which is used to make http requests to the backend.

Now let’s create a folder called `services` in `clients/src` and add a new file called `Api.js` which is going to be a connector between the `client` and the `server.`

In `Api.js`

Let’s create a new file in the same location as `PostsService.js` . This is going to be used for hitting the `posts` api endpoint which we create a while ago in the `server` folder.

In `PostsService.js`

Let’s add a new page for displaying posts.
The `src/router/index.js` should look like this:

Now that we have created a route for posts, let’s add a new view component for that route. Let’s add a new file in `src/components` as `Posts.vue`

When you go to `http://localhost:8080/posts` , you should be able to see this:

![img](https://cdn-images-1.medium.com/max/800/1*VfEp-iBPXnlOPKVDX2z0cw.png)

We don’t need VueJS logo, let’s remove this line of code from `App.vue`

```
<img src="./assets/logo.png">
```

Now let’s call the posts controller from view component posts which lists all the posts that is returned by the posts api endpoint we defined earlier.

Replace the contents of `Posts.vue` with this:

Now when you go to the same location `http://localhost:8080/posts` , you will be able to see this:

![img](https://cdn-images-1.medium.com/max/800/1*VDDr4m14vHiqLVO2WN0LCg.png)

We will be covering the database connection with Mongo and its CRUD operations in the next part.

**Continue to next part: **[(MEVN Application — Part 2/2)](https://medium.com/@anaida07/mevn-stack-application-part-2-2-9ebcf8a22753)

原文地址：[https://medium.com/@anaida07/mevn-stack-application-part-1-3a27b61dcae0](https://medium.com/@anaida07/mevn-stack-application-part-1-3a27b61dcae0)
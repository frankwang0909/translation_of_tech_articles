# MEVN Stack Application [Part 2/2]

This is the continuation of my previous article ([MEVN Application — Part 1/2](https://medium.com/@anaida07/mevn-stack-application-part-1-3a27b61dcae0)) which focuses on creating a boilerplate for an application using Mongo, ExpressJS, VueJS and NodeJS.

This guide will be focused on how to perform CRUD(Create, Read, Update, Delete) operations using ExpressJS and MongoDB(we will be using mongoose).

> If you want to grab the full source code, it is available in Github here: [https://github.com/anaida07/MEVN-boilerplate](https://github.com/anaida07/MEVN-boilerplate)

#### In previous guide we learned to:

- Create a basic skeleton for MEVN application
- Make a connection between backend(ExpressJS) and frontend(VueJS)

#### From this guide you will be able to:

- Perform CRUD operations using Mongoose

In previous article, we left off to creating an application which when routed to `http://localhost:8080/posts` would list all the posts via an Api which we connected by creating a service file`PostService.js` file. 
Let’s run our client and server with `npm run dev`and `npm start`respectively.

Now, if we navigate to `http://localhost:8080` the page should look like this:

![img](https://cdn-images-1.medium.com/max/800/1*JNPPVu61w5yRdosLWg0RmQ.png)Dummy posts lists

Let’s start by creating a connection with mongoose first. In `server/app.js`add the following code:

This will create a connection with a database called `posts`

Now before we dive into the CRUD operations, we need a model which will represent the documents in our Mongo DB. So, let’s start by defining a Schema for our `Post` model:

Create a folder named `models` in `server` directory and add a file named `post.js` and add following code:

Now that we have our model in place, we can begin performing our CRUD operations. Let’s start by requiring our `Post` model in `server/app.js`

```
var Post = require("../models/post");
```

### 1. CREATE

In `server/app.js` , add:

Here, we opened an API endpoint called `posts` which receives the http`POST`method to create a new record for our`Post` model. We can also test this API endpoint using `Postman` or any other tool whichever you prefer. I am using Postman. Here, you should be able to view something like this:

![img](https://cdn-images-1.medium.com/max/800/1*56j_kf681CYzRtYyv-4P8g.gif)Create a Post

### 2. READ

We already have a `GET` endpoint ready to fetch our posts from our previous article. Let’s replace this API code with the following in `server/app.js`:

which fetches all the posts in descending order:

![img](https://cdn-images-1.medium.com/max/800/1*NwjtMMz-v95lfgwvTg3vyg.gif)Fetch all posts

Now that we have the APIs ready for creating and fetching posts, let’s begin adding new posts via the UI:

In `client/src/router/index.js` , add a new route for adding posts:

Now, let’s create a file in `client/src/components` and name it `NewPost.vue`and add following code which renders a form from where we can add new posts:

In `client/src/services/PostsService.js`

What we did till now is we called a method when the user clicks on `Add`button in the form, which via `PostsService` calls the add post API in our server. Let’s also modify our view file which lists all the files:

In `client/src/components/Posts.vue`

Now if we visit `http://localhost:8080/posts` we should be able to see this. We should be able to add the posts and view all the posts listed in the UI:

![img](https://cdn-images-1.medium.com/max/800/1*R0lmOf2Sn7Xthb93stnjOg.gif)Add and Fetch all posts

Now that we have covered CREATE and READ operations, let’s move on to UPDATE and DELETE.

### 3. UPDATE

In `server/app.js` , add:

Let’s add a route in `client/src/router/index.js`

Add a file called `EditPost.vue` in `client/src/components` with the following code:

In `PostsService.js` file:

Now, we should be able to update our posts.

![img](https://cdn-images-1.medium.com/max/800/1*nvvQTYTPwfEX3xFSK64lwA.gif)Update a Post

### 4. DELETE

Similarly, let’s start by creating an API endpoint for deleting our posts:

In `server/app.js` , add:

In `client/src/components/Posts.vue` , let’s call a method when the user clicks `Delete` button.

In `PostsService.js`

Now, we can delete our posts as well:

![img](https://cdn-images-1.medium.com/max/800/1*qTSR1w3P-1-OoQl0iYpwlA.gif)Delete a Post

**Note:**

If you see a `#` in the url you visit([http://localhost:8080/#/posts](http://localhost:8080/#/posts)), you can turn it of by adding `mode: history` in your VueJS route file.

```
export default new Router({
  mode: 'history',
  routes: []
})
```





原文地址： [https://medium.com/@anaida07/mevn-stack-application-part-2-2-9ebcf8a22753](https://medium.com/@anaida07/mevn-stack-application-part-2-2-9ebcf8a22753)
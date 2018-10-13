Being a full-stack developer means we have a lot of things to focus on in order to build a successful application. With so much evolution happening on the front-end, a lot of developers have drifted away from the back-end and dev-ops. That's where MongoDB Atlas and Stitch comes in. MongoDB Atlas is a Database as a Service (DBaaS) that backs Stitch, their Backend as a Service (BaaS). Today I'm going to show how we can interact with MongoDB from our front-end logic without having to spin up an API! The React application we'll be using can be found [here](https://github.com/AimeeKnight/React-Stitch).

The first thing we need to do is to [create our free account](https://www.mongodb.com/cloud/stitch) with MongoDB Stitch. From there, we'll be brought to a page to create our first cluster. As long as we're happy with the default options, it's 100% free! After we proceed, our cluster will start deploying automatically.

![cluster-deploy]({{ site.url }}/assets/stitch/cluster-deploy.png)
![cluster]({{ site.url }}/assets/stitch/cluster.png)

Once we have our cluster, we can create our Stitch app by clicking the link on the left-hand side of the page and then clicking "Create New Application". We'll be prompted to name our application and we'll need to link our Stitch application to the cluster that we just created.

![app-creation]({{ site.url }}/assets/stitch/app-creation.png)

Now that our Stitch application is created we’ll want to create a database and collection. Let's name our database "demo", and our collection "posts", since we’ll be using our Stitch app to populate our React application with post content.

![db-creation]({{ site.url }}/assets/stitch/db-creation.png)
![db-create-success]({{ site.url }}/assets/stitch/db-create-success.png)

For the case of this demo app, let's go ahead and populate our collection with the post document we’ll want to query. To do this, we're going to go back to our cluster and click on "Collections". From there we can insert a simple post document with a title and body.

![document-insert]({{ site.url }}/assets/stitch/document-insert.png)

As with most things in software development, there are a number of ways we can fetch our posts from our React app. The quickest way to get the job done would be to utilize the [Stitch browser SDK](https://www.npmjs.com/package/mongodb-stitch-browser-sdk) which we can install from NPM. With the SDK we can log in, and then query our database collection all from within our front-end logic. The Stitch welcome page even gives us an example snippet with pre-populated code! However, considering best practices in front-end architecture, it would be better for us to make an HTTP call from our React component. With Stitch Services, we can do exactly that!

To set up our endpoint, let's go back to our Stitch app and click "Services" in the left-hand navigation. As you we see, there are a few options to choose from. Since we’ll want to define our own endpoint to make GET calls to, we'll select HTTP and name it whatever we’d like.

![service-creation]({{ site.url }}/assets/stitch/service-creation.png)

Next up we’ll be brought to a page which allows us to configure our webhook, which when called, will execute a function we’ll define next. Because this is a demo application we're going to utilize the default webhook name and choose "Do Not Validate" as our request validation. We're also going to run our webhook as "System" so that for now, we execute our webhook with full privileges, bypassing any rules that may be set up later. Finally, we’ll toggle on "Respond With Result" and use GET as our HTTP method.

![service-config]({{ site.url }}/assets/stitch/service-config.png)

Now we can pop over to the function editor and see some pre-populated logic. Go ahead and remove it, because we want to add our own logic to query for our posts instead. First, we'll use the global context object to access MongoDB Atlas. Once we have a reference to MongoDB, all we have to do is create a reference to our posts collection, and query it. Since this is just a demo, we only want to bring back one result for now.

![service-function]({{ site.url }}/assets/stitch/service-function.png)

Last up, we'll hop back over to our settings, copy our webhook URL, and make a fetch call from our React app to bring back our posts!

![react-stitch]({{ site.url }}/assets/stitch/react-stitch.png)

As we can see, it's pretty nice to communicate with MongoDB Atlas from our front-end logic without needing to maintain our own API layer! I don't know about you, but I really couldn't come up with a better name than Stitch!

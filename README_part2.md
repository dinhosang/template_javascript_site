# Recap of Part 1

So far we have:
  * Built our basic file structure
    * potentially with temp.txt files in our currently empty folders so that they able to be pushed to GitHub
  * Setup a local git repo
  * installed basic npm modules
  * setup our npm scripts
  * setup our webpack config and create our bundle files by running it once
  * created a basic html template in our index.html file and linked our bundle.js to our html by using a script element
  * setup our .gitignore
  * hopefully now git commited a couple times and linked to a GitHub repo!
  * the above was all performed in the master branch to set it up for developement.
  * any future steps should occur on a feature branch, which has been branched from a develop branch, which was itself branched from master. Remember good git practise with regards to merging!

## Post structure first steps for the project - i.e. good things to mob program

* Hopefully as a group you have built the above file structure together. While you are grouped up, it may also be useful to write a few common files together.
* With any of these it is of course up to your group whether you wish to mob program everything in your project, or whether you wish to while mostly pair or solo program, but maybe add a few more features to the mob program list than detailed below. Up to your team.
* Some of the below also relies on your team having discussed your mvp and perhaps having designed good wireframes detailing what contracts exist between the various parts of your site. Please feel free to check the link below for an example of what I mean if you're interested. You'll find an example wireframe contract of a particular page/view which details the elements, ids, what is part of the html file, and what is dynamic from js, and what the page implies for data and view models and what attributes and methods they should have to allow this page to exist (which is the end goal):

![image example wireframe contract for a page/view](https://github.com/dinhosang/template_javascript_site/tree/master/imagesForReadme/exampleWireframeContract.png "example wireframe for a page")

* Files to write up as a group:
  * a __basic server.js__ to listen to the correct port, to access our index.html, (and if you take the last option stated maybe you also wish to write the access to your controllers)
  * the __default html__ elements which are represented by your viewModels, within which will append more dynamic features to this defaut html element.
  * a __basic app.js__ file with just an event of the document loading, and an empty callback.
  * perhaps __controller files with empty paths__ for whatever routes you need for an mvp, just to solidify that everyone is on the same page about what your site is trying to achieve.

---------------
* __server.js__
  * For a basic server.js we need to be able to access our server, and see our html and bundled code!
  * It's as good a place to start as any as without a connection to a server the site will never load when someone tries to access the url.
  * To do so we need to use features from the express module, and link our html page to it as well so that it knows what to display when people hit our homepage.

  * Starting from the top of our server.js file
  ```js
    const express = require('express')
    const server  = new express()
  ```
  * The above code requires in the full express class from the express module
  * We then create a new instance of that class and assign it the name server, as that is what it is for us.
  ```js
    const express = require('express')
    const server  = new express()

    // new
    server.use(express.static(`${__dirname}/build`))
  ```
  * the new lines tells our server that there are files in our build folder that it needs to pay attention to.
  * As a default part of this feature, it knows to treat any file named index.html as our hompage, and automatically creates a GET route for the 'http://localhost:3000/' url, which is why we were were able to see the star wars quotes homepage without having explicitly written a .get('/', callback) route for the homepage.
  * We will still need to write such an index for any other routes, such as for our apis (which are any routes which get information from our database).
  ```js
    const express = require('express')
    const server  = new express()

    server.use(express.static(`${__dirname}/build`))

    //new
    server.listen(3000, function() {
      console.log('Server listening on port 3000');
  })
  ```
  * You may change the port number to something other than 3000, but that's the default we have been using.
  * There is no need to have a callback or to log anything within it. It could be useful though to see that all is working as expected.

  * Now we have a working server, one that will refresh automatically when we change our server side code, thanks to nodemon (a module we installed globally in class).

  ------------
  __index.html__
  * Now would be a good time to setup the basic structure of the site, by hardcoding the html elements that likely won't change very often if at all. This allows them to be targeted (and represented by) the viewModels.
  * If we take a look at the example wireframe posted above, and ![here](./imagesForReadme/exampleWireframeContract.png "example wireframe for a page") again,

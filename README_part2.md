<a name='top'></a>
## Contents
  * [Recap of Part 1](#recap1)
  * [Post Structure Build - First Steps](#post-structure-steps)
    * [Basic Server.js](#server-js)
    * [Index.html](#index-html)
    * [Aside - Wireframe Contracts](#wireframe-aside)
    * [Basic App.js](#basic-app-js)
    * [Empty Route Controllers](#empty-routes)
    * [Recap of Part 2](#recap2)
    * [Final thoughts](#conclusion)

<a name='recap1'></a>
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

<a name='post-structure-steps'></a>
## Post structure first steps for the project - i.e. good things to mob program

* Hopefully as a group you have built the above file structure together. While you are grouped up, it may also be useful to write a few common files together.
* With any of these it is of course up to your group whether you wish to mob program everything in your project, or whether you wish to while mostly pair or solo program, but maybe add a few more features to the mob program list than detailed below. Up to your team.
* Some of the below also relies on your team having discussed your mvp and perhaps having designed good wireframes detailing what contracts exist between the various parts of your site. Please feel free to check the link below for an example of what I mean if you're interested. You'll find an example wireframe contract of a particular page/view which details the elements, ids, what is part of the html file, and what is dynamic from js, and what the page implies for data and view models and what attributes and methods they should have to allow this page to exist (which is the end goal):

![image example wireframe contract for a page/view](./imagesForReadme/exampleWireframeContract.png "example wireframe for a page")

* Files to write up as a group:
  * a __basic server.js__ to listen to the correct port, to access our index.html, (and if you take the last option stated maybe you also wish to write the access to your controllers)
  * the __default html__ elements which are represented by your viewModels, within which will append more dynamic features to this defaut html element.
  * a __basic app.js__ file with just an event of the document loading, and an empty callback.
  * perhaps __controller files with empty paths__ for whatever routes you need for an mvp, just to solidify that everyone is on the same page about what your site is trying to achieve.

  * Don't forget good git practise of creating feature branches when working on the following.

[Return to top](#top)
---------------
<a name='server-js'></a>
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
    //new lines
    const parser  = require('body-parser')

    server.use(parser.json())
  ```
  * The parser allows us to read from the body of a request without having to parse it first, it does that for us
  * We require it in and then .use its json method within our server.
  ```js
    const express = require('express')
    const server  = new express()
    const parser  = require('body-parser')

    server.use(parser.json())
    // new
    server.use(express.static(`${__dirname}/build`))
    server.use(parser.urlencoded({extended: true}))
  ```
  * the new lines tells our server that there are files in our build folder that it needs to pay attention to.
  * As a default part of this feature, it knows to treat any file named index.html as our hompage, and automatically creates a GET route for the 'http://localhost:3000/' url (if that is the port we use), which is why we were were able to see the star wars quotes homepage without having explicitly written a .get('/', callback) route for the homepage.
  * We will still need to write such an index for any other routes, such as for our apis (which are any routes which get information from our database).
  * The parser also comes with a method to handle url encoding (the characters used in urls sometimes like %20 which represents a space in a url). Not exactly sure what it does, just that we may need it! It is likely in classnotes from this earlier this week (week 13). We need to use this in our server just as we have the parser.json and the express.static(build path)

  ```js
    const express = require('express')
    const parser  = require('body-parser')
    const server  = express()

    server.use(parser.json())
    server.use(express.static(`${__dirname}/build`))
    server.use(parser.urlencoded({extended: true}))

    //new
    server.listen(3000, function() {
      console.log('Server listening on port 3000')
  })
  ```
  * For the new lines above you may change the port number to something other than 3000, but that's the default we have been using.
  * There is no need to have a callback or to log anything within it. It could be useful though to see that all is working as expected.

  * Now we have a working server, one that will refresh automatically when we change our server side code, thanks to nodemon (a module we installed globally in class).

[Return to top](#top)
  ------------
  <a name="index-html"></a>
  __index.html__
  * Now would be a good time to setup the basic structure of the site, by hardcoding the html elements that likely won't change very often if at all. This allows them to be targeted (and represented by) the viewModels.
  * If we take a look at the example wireframe posted above, and here again:


  ![image example wireframe contract for a page/view](./imagesForReadme/exampleWireframeContract.png "example wireframe for a page")

  * You may need to zoom in on your browser, or download the image and view in a separate program.
  * If we check it though, we can see that the wireframe on the left details what static elements we have (as well as their css contracts - their ids, which will aid with any future styling efforts by ensuring everyone knows from the beginning what ids were used).
  * From the diagram we can see that our group determined that the:
    * header (which we have id'ed as #banner) is a static element
    * ul (#bucket-list-view), is also a static element, which contains dynamic elements (it is one of our viewModels - given the class name ListView in our planning, part of its contract)
    * form (#add-country-form) is also static. It contains some static elements: a select with a starting option (likely reading something like 'please select a country name'), and a button. One of its child elements contains dynamic elements as well. As such the form is also a viewModel - named FormView as it will be populated using js.
      * the select dropdown (#country-dropdown) contains dynamic elements
      * the button (#submit-country-button) is static, but as an aside its purpose in this project would be achieved via js, not via the default html.
  * With that knowledge we can build our basic html structure. The styling of it will be left as a separate task in our css. Speaking of we should link that css file in our head element:
  ```html
    <link rel="stylesheet" href="styles.css">
  ```
  * If your group feels it is useful to break out the styling into separate files to handle different tasks, a styles folder could be made in the build and the css files moved into there, just be sure to include the relative path in the href for each one.
  * Your static elements will be determined by your project, so further examples will not be detailed here as this repo is to exist as a template for a potential site.

[Return to top](#top)
---------------
<a name='wireframe-aside'></a>
__Aside: Wireframe Contracts__
  * One last slightly lengthy aside for this section, you may have seen the right side of the diagram above shows a contracts list. These are contracts between the various parts of the project that have been determined naturally and declared and refined explicitly by the group during planing, and through the drawing of the wireframe.

  * _(if you wish you can skip to the_ [next section](#basic-app-js)_, though the following paragraphs detail something that I feel is quite useful!)_

  * For instance, at the top of the actual wireframe diagram we can see the goal of this view which is to display a person's bucket list of countries to visit
  * Through this we know that we likely need a person model, and this model should hold a collection of countries. After discussion with the group it is decided that this should be on a this.bucketList attribute, which should return an array of country names - of strings.

  * We also know that this page holds a form for adding countries to our bucketList, so we know the person model should have a method to add to its this.bucketList array. In planning it was decided that it should be called addCountryToList('country name') which should accept a country name as a string and place it in the bucketList on that person instance.
  * Now the group could split and write a viewModel for the list of country names, a dataModel for Person (i.e. the Person class), and a controller route for handing the index call to the /person route which displays that persons bucketList (for basic mvp just have one person, but could modify to be a /person/:id route instead once ability to create and choose an account is added in as an extension maybe).

  * This is possible because everyone knows the expected inputs and outputs, and how those are achieved.
  * The colleague writing the ListView model knows that when that class is instantiated that it will be passed in as an argument a collection of countries which are just strings of country names (this is shown further down the wireframe diagram and detailed in the contracts)

  * The controller writer knows that if they create an instance of Person to interact with the person table in the database that there will exist at the very least the attribute .bucketList which will give the controller the array of country names it needs to pass to the viewModel, and on the CREATE route it knows that the instance of the Person class it has just invoked will have a method for adding that new country name to the database for that particular person.
  * And the individual writing the Person class knows that whatever else they write in their class (which the other parts of the project don't care about), they should have those two features, that return and accept the data types detailed in the wireframe contract, and should achieve the stated goals.

  * If your group handles the project via mob programming this ability to more safely modularise the building of the site amongst different colleagues will not be as important a feature, but it would probably still be useful to help guild the endeavour.

  * __Aside over__

  [Return to top](#top)
---------------
<a name='basic-app-js'></a>
__basic app.js__

  * We have setup a basic server, and linked it to our build files, after which we setup our html file to show its static elements (with ids) and linked in our styles in addition to our earlier script in of our bundle.js
  * Let's set up our app.js which is responsible for controlling the view our users see when they hit our html page by going to our home route '/'.
  * Just a basic one. This can be built up as people finish designing view and data models, or used as a testing ground for checking things in the console browser. Or perhaps we fill in our app.js piece by piece only writing a viewModel as we come to it in our process of filling out our app.js. Whatever your group decides lets at least get a basic template going.
  * In app.js (hopefully located in your /src/ folder)
  ```js
    document.addEventListener('DOMContentLoaded', main)
  ```
  * Nothing can happen in terms of our dynamic javascript that faces our user, until we know the user has loaded the page. So first we set up an event listener on our page (the document) so that we're prepared to start the ball rolling once someone hits our site.
  * We've given the eventListener a callback named main, but currently this doesn't exist in our file. Now that we have named it, we need it. So let's write it.
  * Make sure you have webpack running if you wish to see anything logged out when you hit the homepage, as we are now modifying client side code so we need webpack to be continually bundling what we're writing.
  ```zh
    npm run webpack
  ```
  * Code to write to app.js
  ```js

    // new
    const main = function() {
      console.log('site loaded')
    }

    // already written
    document.addEventListener('DOMContentLoaded', main)
  ```
  * Now we have our app.js set up which will manage all the events we want to allow our users to perform. In the earlier wireframe diagram that meant being able to hit the homepage and see a list of countries in the bucket list, and being able to click a submit button to add a country.
  * Those functionalities could be performed by the viewModels in some fashion, perhaps being given a callback when they are constructed which is then able to send a request when the appropriate event occurs (like the button click)
  * Those request will hit the /api/whatever routes detailed in the controllers, the controllers would then interact with our data models which in turn could interact with our database and return the information the controller sought, which the controller then sends pack on the response to the appropriate source whether that be the viewModel or the app.js depending of your project structure.

[Return to top](#top)
---------------------
<a name="empty-routes"></a>
__empty controller routes__
* It should not be necessary if full wireframe contracts (or some other similar understanding) were made, but it could be useful to create the expected controllers for the MVP and empty paths for them just to make sure everyone is aware of what is going on, maybe with comments for what is expected to return in terms of status and body, and what is expected to be sent in the original request as well.
```js
  const express   = require('express')
  const parser    = require('body-parser')
  const whateverRouter = new express.Router()
  const Whatever  = require('../dataModels/whatever.js')

  // may not require the below parser.json line because the server combines this within itself in our server.js file, and the server uses this controller so perhaps all is well.
  // In which case we won't need the require('body-parser') in this file either, as it's handled in the server.js.
  // of course we need to make sure that we have our server.js use this controller and assign it the appropriate path
  whateverRouter.use(parser.json());

  // INDEX route - (R)
  whateverRouter.get('/', function(req, res) {
    // create instance of whatever

    // use the method detailed in contract to get require information

    // maybe have a check to see if the data model reports an error connecting to the database

    // manipulate data as needed

    // add data to body of res, log to console and send res if needed (not required if using res.json(data) as that sends it automatically)
  })
  // ...

  module.exports = whateverRouter;
```
  * If following the above idea of detailing out empty routes that you know the project will need for mvp (which may not be all of them), then be sure to add this controller to the server in server.js (as mentioned in the comments regarding the parser.json)
  * In server.js
  ```js
    // already written
    const express = require('express')
    const parser  = require('body-parser')
    const server  = express()

    server.use(parser.json());
    server.use(express.static(`${__dirname}/build`))
    server.use(parser.urlencoded({extended: true}));
    // new
    server.use('api/whatever', require(`${__dirname}/src/controllers/whateverApiController.js`))

    // already written
    server.listen(3000, function() {
      console.log('Server listening on port 3000');
    })
  ```
  * The new lines makes our server aware of our controller (which has only empty paths currently but is still a controller)
  * If we have many controllers we could choose to create a central controller in the controllers folder that is then used by the server, to aid in readability and maintainability of the server.js file.

[Return to top](#top)
-----------
<a name='recap2'></a>
__RECAP Part 2__

We have now:
  * built a basic server.js file that can listen to a port and receive visits to the home route
  * built a basic html template with our static elements that our viewModels will hook onto to and represent
  * built a basic app.js file that is now ready to await user events and invoke our viewModels
  * maybe - some controller files with empty routes, but with comments about purpose and response, which have been linked to the server in our server.js file.

[Return to top](#top)
-------------------
<a name="conclusion"></a>
## What's Next?

That's up to the group, hopefully planning and design have been performed, and there is an idea of how you all will be working towards the MVP.

As part of the planning the group has hopefully also considered what contracts need to exist between the various parts of your project so that if you work alone or in pairs that the parts you build have the correct hooks and expect the correct data so that they can integrate more comfortably together. Even if working through mob programming, it would be good to have a clear view about what each part is expecting, and expected to do, before starting to program.

The inner workings don't need to be figured out, just the expected input and output, that is the basic requirement for ensuring you can fit your pieces together.

Don't forget good git practise!

Of course everything detailed here is just one opinion. It's one I've started to form form conversation with and listening to fellow students and our instructors, but an opinion is all it is. I simply hope this is of some use to people, even if just as a stepping stone to something more fully fleshed out.

If it does lead that way please let the rest of us know your findings too!

[Return to top](#top)

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

![image example wireframe contract for a page/view](./imagesForReadme/exampleWireframeContract.png "example wireframe for a page")

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

  * __One last slightly lengthy aside for this section__, you may have seen the right side of the diagram above shows a contracts list. These are contracts between the various parts of the project that have been determined naturally and declared and refined explicitly by the group during planing, and through the drawing of the wireframe.

  * _(if you wish you can skip to the next bolded text, though the following paragraphs detail something that I feel is quite useful!)_

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
---------------
__basic app.js__

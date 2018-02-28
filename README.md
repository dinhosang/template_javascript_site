# A guide to a basic project setup

## Create file structure

In below section any sub points are to signify files/folders created within the above folder.

At this point the goal is to only create **empty** folders and files. The structure of a project.

A 'feature' of GitHub is that it does not push empty folders.

If you wish the full file structure to be seen by everyone and not just the first creator then an empty placeholder text file could be created in each empty folder which may be removed after the first true file is entered.

* create project_folder
  * create a 'build' folder
    * create index.html file
    * create styles.css file
  * create an 'src' folder
    * create helpers folder
      * (create temp.txt file)
    * create viewModels folder
      * (create temp.txt file)
    * create dataModels folder
      * (create temp.txt file)
    * create controllers folder
      * (create temp.txt file)
    * create app.js file
  * create specs folder
    * (create temp.txt file)
  * create server.js file
  * create webpack.config.js file
  * create .gitignore file


Example below:

![image of file structure after setup](https://github.com/dinhosang/template_javascript_site/tree/master/imagesForReadme/afterStructureBuilt.png "example structure")

## Git setup

This could also be done before creating the above file structure. In that instance at least one file must exist to be able to git commit -A and then git push. It won't work unless the folder contains something - for instance a README.md if desired.

* At top level of file structure
```sh
git init
```
* (still in master branch)
```sh
git add -A
git commit -m "first commit"
```

## NPM setup + package.json scripts, .gitignore, & html

There are a few modules we need to bring in, and a few files we'll need to modify. Some will be dev-tool side only.

We should already have nodemon installed globally so no need to worry there. Some will also require us to edit the package.json file scripts a little.

* __npm__ (to be able to access the below modules)
* __express__ (for our server and controllers)
* __mocha__ (for our testing of our data models)
* __webpack__ (to allow for the bundling of our src code and keeping the live views up to date with our newly written code - or something)
  * __webpack__ may also require an additional install, will be detailed later
* __mongodb__ (to allow for an easier interface with our mongodb server and the database within)
* __body-parser__ (to allow us to convert request bodies from JSON to js automatically)


__Note__: any point where the following is written -

```sh
npm install <module>
```
that is the same as writing -
```sh
npm install --save <module>.
```


__But__, when saving as a __dev__ dependency then the full command must be used instead:

 ```sh
 npm install --save-dev <module>
```
-------------

* __Npm__

  * In the top level of our file structure (true for all the following modules as well)
```sh
  npm init -y
```
  * the -y auto-accepts all the defaults for npm, saving you having to enter multiple times just to accept them.
  * this command prepares npm for our project. We will need to setup our .gitignore on the modules, but we will leave that to the end.
  * If you are cloning from a repo that has already run through this and all the below modules, then remember you should only use the following command to get all your npm modules:

    ```sh
      npm install
    ```

* __Express__
```sh
  npm install express
```
  * this is a production dependency and so should be installed using using install or install --save as per your personal preference.
  * production dependency meaning that it will still be in use when our code is live to our users


* __Mocha__
```sh
npm install --save-dev mocha
```
  * this is a development dependency (a dev tool), meaning it should be installed using the --save-dev syntax
  * dev tool meaning that it is only used when a project is marked as being in development.
  * We haven't been shown how to switch from development (the default) to production yet, but I have seen what appears to be an option in webpack.


* __Webpack__
  * First, we should setup the webpack.config.js file that resides in the top level of our project structure.
  * If the structure detailed in the beginning has been followed, then the below code can be pasted into the webpack.config.js file:

  ```js
    config = {
      entry: __dirname + "/src/app.js",

      output: {
        filename: "bundle.js",
        path: __dirname + "/build"
      },

      devtool: 'source-map'
    }

    module.exports = config;
  ```
    * read following paragraphs to check what each part is doing or jump ahead to next bolded line:

    __Rundown of config file components:__
    * the entry is signifying the location of our main view - in our case our app.js file. Everything is then bundled up from that point, with webpack looks at everything app.js requires in, and then what those require in and so on.
    * the output is stating what it should call the file it creates after bundling all our code files up (we use bundle.js) with the path being where we should place that file (in our case in our build folder alongside our html)
      * as a follow on to above, in a future step we will need to script in our bundle.js file into our html file, in the head section. We'll reach that point later in this guide.
    * the devtool is asking what we should name the file it makes that is a slightly more readable to human eyes version of the bundle.js, this is stored in the same directory as the bundle.js (the build folder for us)
    * then we export it for use elsewhere (our html file for instance)

    __Back to Setup__
    * Now we have setup our webpack.config.js file, we should install webpack.

    ```sh
    npm install --save-dev webpack
    ```
    * There is another file to install, otherwise it will fail when you try to use npm run webpack (we'll get to that below). It does give you the command to use to install the cli dependency when it fails the run.
    * this appears to be some kind of additional dependency that is missed out of the main webpack. Feel free to wait on installing this until after we try to run webpack below, just incase the command or the situation has changed.
    * For reference, the command you will be asked to use should take the form (at least at this date: 28/02/2018):
    ```sh
    npm install webpack-cli -D
    ```
    * Again you may wish to wait on the above -cli install until you see it request it on trying to run webpack in the later segment on package.json.


* __Mongodb__
```zh
npm install mongodb
```
  * This gives us an interface for connecting to our mongo database more easily. It gives us access to an object with methods we can use to connect to and interact with our mongo server.

* __Body-parser__
```zh
npm install body-parser
```
  * This allows us to parse the body of http requests from JSON to js automatically.

* __package.json__
  * We now need to setup our package.json file correctly. It is located in whatever level of our project that we ran npm init in.
    * Which was hopefully the top level where your build, src, and specs folder where made as well are your server.js, .gitignore, and any other files or folders made by the npm modules.
  * In the scripts part of the package.json file we need to modify the "test" and "start" scripts, and add a new "webpack" script. Please keep in mind the commas if there are other scripts past the one you are writing.

  * __"test"__
    * This should redirect to your specs folder:

    ```json
    "test": "mocha ./specs",
    ```
  * __"start"__
    * This should use nodemon, rather than node. This means we can update our server side code, and not have to restart our server like we did with Sinatra.

    ```json
    "start": "nodemon server.js",
    ```
  * __"webpack"__
    * We need to create a webpack script that will bundle our code, and keep rebundling it so our html file can see the changes whenever we update our front end code.

    ```json
    "webpack": "webpack -w"
    ```
  * __Note__ on using scripts
    * test and start are default scripts and can be used just by typing:
    ```zh
     npm test
     npm start
    ```
     This can be done even if we change what they do (like using nodemon instead of node)
    * webpack is one we created ourselves (and it can be named anything, webpack is just clean for me), and to use it we must type (replace webpack with whatever name you like as long as it matches your script key):
    ```sh
    npm run webpack
    ```

  *  Finally, lets run the webpack so that it can create the bundles. We can shutdown the webpack straight after if you like, we only need to run it now to check we set it up correctly and that the bundle files have been made. After which we can ready our .gitignore and commit what we've done (details to follow).
  * type in the command for running webpack:

  ```sh
  npm run webpack
  ```
  * You should now see a bundle.js file and bundle.js.map file in our build folder.


* __.gitignore__
  * We should now setup our .gitignore file to ignore the features that people should be able to grab on their own through their npm init. This makes sure they any using our rep have up-to-date modules, and that our repo isn't filled with unneeded files and folder.
  * In same level as our .gitignore (or elsewhere with the correct relative filepath):

  ```zh
  echo "node_modules" >> .gitignore
  echo "/build/bundle.*" >> .gitignore
  ```
  * We are now ignoring the node_modules folder, and we are also ignoring any files named bundle.(whatever) that are located in the build folder. This means we are ignoring both bundle.js, and bundle.js.map.
  * We would also ignore anything like bundle.py, bundle.txt, bundle.html that happened to exist in the build folder.
  * That is unlikely to be the case, but keep it in mind, and write it the two files names out separately in the .gitignore if that becomes a concern.

* __Html__
  * To tie up the webpack business, we should script that bundle.js file into our index.html
    * Note that it is important that we call our main html file index.html. Ask Alex if you want more details on that! Think of it as a convention that in particular our express module expects if we use certain features of it.
  * In our (currently empty) index.html setup a basic html template by typing html and then letting atom (or whatever you use) autocomplete with tab or whatever button you have it set to. Then within the <head> element type the following:

  ```html
  <script src="bundle.js"></script>
  ```
  * because bundle.js is in the same folder as our index.html we don't need to write a more detailed filepath.

* Now feels like a good time to git again.
```zh
git add -A
git commit -m "installed npm modules, written .gitignore, and scripted bundle.js into index.html"
```
* If you've not already pushed to a GitHub repo now would also be a good time for that.

-------------
__Recap__

So far we have:
  * Built our basic file structure
  * Setup a local git repo
  * installed ba

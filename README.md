# My Learnings

## Tools

* How to write notes using Markdown : [Mastering Markdown](https://guides.github.com/features/mastering-markdown/)
* Quickly type HTML using Emmet `ul>li.item$*5` : [Emmet Cheat Sheet](http://docs.emmet.io/cheat-sheet/)
* Github
  * Either takes existing project (in local system for example) and import into Git
    * Do `git init` which creates .git hidden folder that maintains versioning of local files
    * Then create a new repo in github.com which will give a command similar to `git remote add origin https://github.com/braincap/fresh.git`. This is a remote which needs to be run in local system cmd; inside the working folder where init was done. This connects local repo to server.
    * `git add .` to add all files in the folder to staging area. Only these files will be committed
    * `git commit -m "informational message"`  then commits all files in staging area and confirms local changes
    * `git push` then pushes all files to git server in github.com. Give your git account's credentails to connect to github server
    * `git status` anytime keeps checking status of local
  * Or clone an existing repository that's in github.com into local system for working on it
    * Use `git clone https://github.com/braincap/existing.git`
    * [Host create-react-app in Github Pages](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#github-pages)
* Hosting on Heroku

  Sequence | Command | Purpose
  ---------|---------|--------
  1.|Download and install in system|To use in local development
  2.|`heroku login`|Login into heroku account
  3.|`heroku create`|Prepares heroku to receive code and add git remote `heroku`
  4.|`git push heroku master`|Deploys the code to heroku
  5.|`heroku ps:scale web=1`|Run one instance of the app
  6.|`heroku open`|Opens the website
  7.|`heroku logs --tail`|View logs
  8.|`echo web: node index.js > Procfile`|Procfile is a text file with command to execute when running the app
  9.|`heroku ps`|Shows running dynos
  10.|`heroku local web`|Starts the app locally
  11.|git add>commit>push (as 4)|Push local changes
  
  * Storing credentials securely for mongodb and the likes
    * Locally for dev and testing (to avoid exposing to public on github)
      1. Create a .env file in root folder and include `.env` in your `.gitignore`
      2. Store data like `MONGOLAB_URI="mongodb://username:password@ds157040.mlab.com:57040/shorturl"`
      3. `npm install dotenv --save`
      4. `require('dotenv').config()` very early in the code
      5. Get this value now anywhere in your code using `var url = process.env.MONGOLAB_URI;`
    * Hosting server (heroku) for production
      1. Now except storing this in .env file which stayed locally, save this value in server using `heroku config:set MONGOLAB_URI=mongodb://username:password@ds157040.mlab.com:57040/shorturl`
      2. Rest steps from 3 to 5 are same

## Node

* Using external libraries/scripts etc through CDN (`<script src='blah blah'></script>` inside `<body>`) is a bad idea because
  * going to each module's github / website and
  * keeping track of and updating their versions is painful
* NPM solves these issues
* `npm init` inside the project directory creates `package.json` to keep track and also assists in installing all packages when someone new runs `npm install`
* `npm install jquery --save` will install jquery. `node_modules` folder gets required modile files and `--save` makes sure `package.json` gets other metadata to track module versions etc
* Webpack `npm install webpack` needs to know the `entry` point of your application, needs to know which transformations `module` (like SASS/LESS/CoffeeScript) to make on your code and needs to know to which location `output` it should save the new transformed code
  * Dev is done in `/app` folder. Webpack does loader based transformations and spits out processed files in `output` directory which is `dist` in this case for example.
  * To copy `index.html` from `/app` to `/dist` everytime a change occurs as part of dev, use `npm install --save-dev html-webpack-plugin` tool (aka _plugin_)
  * This tool also injects output script of webpack loader into the html of `output` folder
  * Create `webpack.config.js` in root folder

    ```javascript
    // In webpack.config.js
    var HtmlWebpackPlugin = require('html-webpack-plugin')
    var HtmlWebpackPluginConfig = new HtmlWebpackPlugin({
        template: __dirname + '/app/index.html',
        filename: 'index.html',
        inject: 'body'
    });

    module.exports = {
        entry: [
            './app/index.js'
        ],
        output: {
            path: __dirname + '/dist',
            filename: "index_bundle.js"
        },
        module: {
            loaders: [
                { test: /\.js$/, exclude: /node_modules/, loader: "babel-loader", query: {presets: ['react']} }
            ]
        },
        plugins: [
            HtmlWebpackPluginConfig
        ]
    };
    ```
  * Babel `npm install --save-dev babel-core babel-loader babel-preset-react` is a loader which also does JSX -> JS transformations. It needs to be added to webpack as one of the loaders.
    * To tell Babel loader that it needs to use React transformation, a `.babelrc` needs to be created in root for that.

    ```javascript
    // In .babelrc file. Use (echo '{ "presets": ["react"] }' > .babelrc)
    {
        "presets": [
            "react"
        ]
    }
    ```
  * In `package.json`'s `scripts`, add `"start": "webpack-dev-server"`. This enables `npm run start` which does (a) Webpack thing followed by (b) run server for http://localhost:8080/
  * As of 3/30/2017, node or browser doesn't support `import x from './z'` which is ES6 feature. Use babel in node to compile if needed

* ## Summary of required installations in root

  Sequence | Command | Purpose
  ---------|---------|--------
  1.| `npm init` | Initialize package.json and /node_modules
  2.| `npm install jquery --save` | Install any package/module. Same for react and react-dom
  3.| `npm install --save-dev webpack webpack-dev-server` | webpack for bundling
  4.| `npm install --save-dev html-webpack-plugin` | html plugin to mirror html file with webpack's output script fed in into `<body>`
  5.| `npm install --save-dev babel-core babel-loader babel-preset-react` | Installs Babel. This is for the JSX -> JS transformation. With Webpack you tell it which transformations to make on your code, while Babel is the specific transformation itself. Sort of mapper vs the thing itself
  6.| `webpack.config.js` | Create webpack config file. Code given above
  7.| `echo '{ "presets": ["react"] }' > .babelrc` | Skip this and use query inside babel module loader
  8.| `webpack -w` | Keep watching for changes once all above steps are done
  9.| `npm install --save-dev html-webpack-plugin webpack webpack-dev-server babel-core babel-loader babel-preset-react` | 1 liner install for dev
  10.| `npm install --save react react-dom` | 1 liner install for all env

## React

* Pass anything to a component while creation using `<Avatar name="Jai"/>`. Use this name inside Avatar component through `this.props.name`
* Pure function [f(d)=v] doesn't depend nor change external variables and gives same output for same parameters everytime without depending on any previous state
* `this.props.children` gives access to element between tags of a component
```javascript
  <Clock>
    <Time />
    <Period />
  </Clock>
 ```
 * ReactRouter activates components based on currect path. For `path='/'`, `{Main}` component is always ALWAYS activated. This can be used for Header/Footer. `IndexRoute` is activated when no other `Route` is activated
 * `{Main}` is reponsible to display its child routes. Hence main component should have `{this.props.children}` in it
 * Basic idea: At any one point of time, one path/route is activated. The component in that route becomes the only "children" of {Main} and thus is displayed
 
 * If a component doesn't have any state, it can also be declared as a "Stateless Functional Component"
```javascript
 function HelloWorld (props) {
  return (
    <div>Hello {props.name}</div>
  )
}
ReactDOM.render(<HelloWorld name='Tyler' />, document.getElementById('app'))
```
* [`propTypes`](https://facebook.github.io/react/docs/typechecking-with-proptypes.html) ensures typechecking of component's props
* Use axios for Ajax requests in componentDidMount
```javascript
  componentDidMount: function () {
    axios.get('https://fcctop100.herokuapp.com/api/fccusers/top/recent')
      .then(results => {
        this.setState({
          recentData: results.data
        });
      });
  },
```
* React's props and states are passed from parent to child via props. If anything needs transmission from child back to parent, use callback functions. Create function in parent and send them to child via props. Child can call the this.props.passedfunc() which will run like it was run by the parent component. This can not only go to child -> parent but also through multi-level hierarchy. In [Recipe Box project](https://braincap.github.io/recipe_box/), "Edit" button is bound to innermost "Recipe" component. But since we need to change the state of outermost parent as part of the edit, all data from "Edit" form was transmitted Recipe>RecipeList>App using callback functions as props

## DigitalOcean VPS configuration

### All these steps as present in DigitalOcean tutorials
* Created a Droplet which is a virtual cloud based Ubunty machine
* Installed putty to access this cloud machine from local system
* Added own user with SSH keys to avoid using root user everytime
* In namecheap.com, changes nameserver to digitalocean's nameservers
* In digitalocean domain configuration, added A and CNAME records to point randomcoder.me to droplet's IP address
* Installed nginx in machine as a webserver


## Random notes

* Imperative (we tell How do) vs Declarative (we tell What to do) code. React it declarative For The Most Part. Part where we do `this.setState` makes it NOT totally declarative. Declarative part of it comes from its components
* [Javascript Modules](https://medium.freecodecamp.com/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#.fs5jv4a77)
* https://docs.nodejitsu.com/
* [MongoDB connection creation](https://mongodb.github.io/node-mongodb-native/driver-articles/mongoclient.html#mongoclient-connection-pooling)
  ```javascript
  var express = require('express');
  var mongodb = require('mongodb');
  var app = express();

  var MongoClient = require('mongodb').MongoClient;
  var db;

  // Initialize connection once
  MongoClient.connect("mongodb://localhost:27017/integration_test", function(err, database) {
    if(err) throw err;

    db = database;

    // Start the application after the database connection is ready
    app.listen(3000);
    console.log("Listening on port 3000");
  });

  // Reuse database object in request handlers
  app.get("/", function(req, res) {
    db.collection("replicaset_mongo_client_collection").find({}, function(err, docs) {
      docs.each(function(err, doc) {
        if(doc) {
          console.log(doc);
        }
        else {
          res.end();
        }
      });
    });
  });
  ```
 * [D3 V4 Force directed graph tutorial](https://roshansanthosh.wordpress.com/2016/09/25/forces-in-d3-js-v4/)

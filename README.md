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
    var HTMLWebpackPluginConfig = new HtmlWebpackPlugin({
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
        {test: /\.js$/, exclude: /node_modules/, loader: "babel-loader"}
        ]
    },
    plugins: [HTMLWebpackPluginConfig]
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

## Random notes

* Imperative (we tell How do) vs Declarative (we tell What to do) code. React it declarative For The Most Part. Part where we do `this.setState` makes it NOT totally declarative. Declarative part of it comes from its components
* [Javascript Modules](https://medium.freecodecamp.com/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#.fs5jv4a77)
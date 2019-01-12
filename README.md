# Webpack babel tutorial
In this tutorial we will learn some basic webpack/babel configuration. We will create from scratch webpack based js application which uses babel as a transpiler.
As a package manger we will use [Yarn](https://yarnpkg.com/en/docs/install).
## Initialize the Project:
In your terminal run
 ```
    mkdir webpack-babel-tutorial
    cd webpack-babel-tutorial
 ```
 Then init your project with npm. To initialize a projectyou can use npm init command. This will create a package.json file. Flag `-y` lets you skip all questions that you will be asked.
 In your terminal run:
  ```
npm init -y
  ```

 Now your package.json will look something like this.
 ```
{
    "name": "webpack-babel-tutorial",
    "version": "1.0.0",
    "main": "index.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "keywords": [],
    "author": "",
    "license": "ISC"
}
```
## Install webpack
**Webpack** is a module bundler that lets us bundle our project files into a single file for production. To install webpack run 
```
yarn add  webpack webpack-cli -D
```
 Flag `-D` will tell yarn to install packages as a devDependencies. webpack-cli will let us use webpack from the command line

## Install Babel
```
yarn add @babel/core babel-loader @babel/preset-env -D
```

* **babel-core**: Babel compiler core.
* **babel-loader**: loader for webpack.
* **babel-preset-env**: Preset taht converts ES6, ES7 and ES8 code to ES5.

## Create main files
We are going to need **index.js** as a entry point file, **index.html**.
Let's create **src** and **public** folders. In you terminal run:
```
mkdir src public
```
Now let's create **index.js** and **index.html** files in **src** and **public** folders respectively.
Run:
```
touch src/index.js public/index.html
```
Add folowing basic html markup to **index.html** file
```
<!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Wepack babel tutorial</title>
    </head>
    <body></body>
</html>
```
## webpack.config.js
Create **webpack.config.js** file on the root level of you project. 
```
touch webpack.config.js
```
Add folowing code to file you just created
```
const path = require("path");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.join(__dirname, "/dist"),
    filename: "bundle.js"
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        },
      }
    ],
  },
};
```
Let's have a look on code above:
* **entry** tells webpack which file should be used as an entry point.
* **output** tells webpack location and name of the file which is going to be compiled.
* **module** tells webpack rules how to procces input files:
  * **test**:  process files with **js** extensions
  * **exclude** do not proccess files fron node_modules folder
  * **use** apply during proccesing babel-loader which we installed before.

## .babelrc

Create .babelrc file in the root
Add following code to file:
```
{
  "presets": ["@babel/preset-env"]
}
```

We installed @babel/preset-env before. This preset is going to be used to transpile the ES6/ES7/ES8 code to ES5.

## Html-webpack-plugin
This plugin will generate html file that we will send to the browser. It's going to be file based on our **index.html** template and  **bundle.js** will be injected automatically
```
yarn add html-webpack-plugin -D
```
Then add folowing code to **webpack.config.js** after **module** property:

```
const HtmlWebpackPlugin = require("html-webpack-plugin");
...
plugins: [
    new HtmlWebpackPlugin({
        template: "./public/index.html"
    })
]
```
`template: "./public/index.html"` tells path to our **index.html** template.

So our  **webpack.config.js** should be this:
```
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.join(__dirname, "/dist"),
    filename: "bundle.js"
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        },
      }
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./public/index.html"
    })
  ]  
};
```
## Compile files by using webpack
At this stage we should be able to use our webpack with its configuratiob and babel to compile files.
Let's add scripts to our **package.json** files, in `"scripts"` sections add folowing commands:

```
"start": "webpack --mode development --watch",
"build": "webpack --mode production"
```
* **start** is going to be used in **development** mode, flag **watch** whenever files are changed webpack will automatically recompile files
* **build** is going to be used in **production** mode (generate optimized bundle)

1. Run:
    ```
    yarn start
   ``` 
2. Open in the browser **index.html** in dist folder.
3. Add to your **index.js** file `console.log('Hello world')` and save file.
4. Refresh page in the browser and check its console in dev tools.

You should see 'Hello world' in your console.

## Webpack-dev-server
To make your development process easier let's use webpack-dev-serve.

1. Add **webpack-dev-server** as a devDpendency, run: 
    ```
    yarn add webpack-dev-server -D
    ```
2. Modify the package.json start script:

    `"start": "webpack-dev-server --mode development --open --hot"`

    **--open** and **--hot** flags will launch browser and refresh the page whenever you change files.
3. Run:
    ```
    yarn start
    ```
## Add styles
To use css files in our javascript code we need to install **css-loader** and **style-loader** as a devDependencies. 
1. Run:
    ```
    yarn add css-loader style-loader -D
    ```
2. In **webpack.config.js** file add new rule:
    ```
    {
        test: /\.css$/,
        use: ["style-loader", "css-loader"]
    }
    ```
    Now your webpack rules should be this:
    ```
    rules: [
        {
            test: /\.js$/,
            exclude: /node_modules/,
            use: {
                loader: "babel-loader"
            },
        },
        {
            test: /\.css$/,
            use: ["style-loader", "css-loader"]
        }
    ],
    ```
3. Create **index.css** file in src folder. Run:
    ```
    touch src/index.css
    ```
    add folowing styles to the file you created:
    ```
    body {
        background: #06c;
    }
    ```
4. Import **index.css** in your **index.js** file:   `import './index.css';`.
5. Yarn start.

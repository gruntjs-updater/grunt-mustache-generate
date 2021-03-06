# grunt-mustache-generate v1.3.1 [![Build Status](https://travis-ci.org/boycott/grunt-mustache-generate.svg?branch=master)](https://travis-ci.org/boycott/grunt-mustache-generate)

> Grunt task to generate html pages and optionally partials for reuse client side.

## Getting Started
This plugin requires Grunt `>=0.4.0`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-mustache-generate --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-mustache-generate');
```

### Settings

There are a number of options available. Please review the [minimatch options here](https://github.com/isaacs/minimatch#options). As well as some additional options as follows:

#### files
Type: `String|Array`

This defines what file patterns this task will watch. Can be a string or an array of files and/or minimatch patterns.

#### options.globalData
Type: `String`

This defines the location of a JSON file containing settings shared across all mustache pages.

#### options.dontMinify
Type: `Boolean`

Don't compress pages.  

#### options.partials
Type: `Object`

This defines the mustache partials if used.

#### options.partials.src
Type: `Array`

Base directories containing mustache partials (task recursively searches within these directories).

#### options.partials.dest
Type: `String`

If you want to output the partials as a Javascript consumable, set a target filename (without file extension).

#### options.partials.varName
Type: `String`

By setting a variable name, the partial output will be saved as a .js file with the varName equal to the template object.
(If you don't set this, the partial output will be a .json file)

#### options.partials.dontMinify
Type: `Boolean`

Don't compress partials.  

#### options.dataDir
Type: `String`

Page data is by default looked for in the same directory as the mustache pages. If desired the json can be contained in a separate directory.

#### options.env
Type: `String`

If used, the json will be first searched for using the format `<page>.<env>.json` If no file exists or no environment is set, fall back to `<page>.json`

#### options.output
Type: `String`
Default: `.html`

Set the page output file extension.

#### options.logLevel
Type: `Integer`
Default: `1`

Set the logging levels:
  0 = no logging.
  1 = log pages.
  2 = (and) log partials.

## Example Grunt settings

```js
mustacheGenerate: {
  options: {
    globalData: 'site.json',
    partials: {
      src: ['partialDir', 'other_partialDir'],
      dest: 'target/partials',
      varName: 'myNS.partials'
    },
    dataDir: 'data',
    env: grunt.option('env') || process.env.GRUNT_ENV,  
    output: '.html',
    logLevel: 2
  },
  files: {
    expand: 'true',
    cwd: 'pages/',
    src: '**/*.mustache',
    dest: 'target'
  }
}
```


## Data Options
Each data file can pull in external data for itself and for child data files.

Specify the location of the data in a special base level object called "copy".  This object contains a reference to each chunk of data, which itself needs to be valid JSON data, either raw in a json file (you don't need to add a file extension), as the value of a javascript variable, or a jsonp response object.

### JSON Data
*messages.json*
```json
{
    "alerts": {
        "global": {
            "header": "Global Message",
            "message": "Global field message"
        },
        "field": {
            "header": "Field Message",
            "message": "Field level message"
        }
    }
}
```
*site.json*
```json
{
    "copy": {
        "messages": "messages"
    }
}
```
*pages/index.json*
```json
{
    "messages": {
        "alertInfo": true,
        "alertHeader": "`messages.alerts.global.header`",
        "alertMessage": "`messages.alerts.global.message`"
    }
}
```

### JS Variable
*messages.js*
```js
NS.messages = {
    "alerts": {
        "global": {
            "header": "Global Message",
            "message": "Global field message"
        },
        "field": {
            "header": "Field Message",
            "message": "Field level message"
        }
    }
};
```
*site.json*
```json
{
    "copy": {
        "messages": "messages.js"
    }
}
```
*pages/index.json*
```json
{
    "messages": {
        "alertInfo": true,
        "alertHeader": "`messages.alerts.global.header`",
        "alertMessage": "`messages.alerts.global.message`"
    }
}
```

### JSONP Response
*messages.js*
```js
NS.messages({
    "alerts": {
        "global": {
            "header": "Global Message",
            "message": "Global field message"
        },
        "field": {
            "header": "Field Message",
            "message": "Field level message"
        }
    }
});
```
*site.json*
```json
{
    "copy": {
        "messages": "messages.js"
    }
}
```
*pages/index.json*
```json
{
    "messages": {
        "alertInfo": true,
        "alertHeader": "`messages.alerts.global.header`",
        "alertMessage": "`messages.alerts.global.message`"
    }
}
```

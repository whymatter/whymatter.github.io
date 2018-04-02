---
layout: post
title:  "What does webpack do?"
date:   2018-04-02 02:02:00PM
custom_css: big-page
categories: js typescript webpack bundler module es5 es6 __webpack_require__
---

'What the heck does webpack ([https://webpack.js.org](https://webpack.js.org)) do?'
One of my biggest fears in my current projekt was to use a module bundler without the support of angular cli.
And so I thought lets have a look inside the ominous bundle.js.

![deepdive]({{ "/assets/images/deepdive.gif" | absolute_url }})

Task of my webpack should be to bundle all ts files, transpile them and output a bundle.js which I can include in my webpage.
Our webpack.config.js looks like this.

{% highlight javascript %}
const path = require('path');

module.exports = {
    // the entry module (will be explained later)
    entry: './src/main.ts', 
    // pretty print the bundle.js
    'mode': 'development',  
    module: {
        rules: [
            {
                // load all .ts files ...
                test: /\.ts$/,     
                // ... using the ts-loader ...      
                use: 'ts-loader',        
                // ... but exclude the node_modules folder
                exclude: /node_modules/  
            }
        ]
    },
    resolve: {
        extensions: ['.ts']
    },
    output: {
        // name the output bundle.js
        filename: 'bundle.js',                
        // write bundle.js into the dist folder of you project root
        path: path.resolve(__dirname, 'dist') 
    }
};

{% endhighlight %}

Most parts should be pretty straight forward but lets have a look at what the end result has to do with my configuration.

So for testing I start with my `./src/main.ts`

{% highlight typescript %}

import { Clip } from './models/clip';

const clip: Clip = new Clip('Webpack.movie');

{% endhighlight %}

And of course `./models/clip.ts`

{% highlight typescript %}

export class Clip {
    constructor(public description: string) { }
}

{% endhighlight %}

Lets run webpack and see what bundle.js looks like


{% highlight javascript %}

/******/ (function(modules) { // webpackBootstrap
/******/ 	// The module cache
/******/ 	var installedModules = {};
/******/
/******/ 	// The require function
/******/ 	function __webpack_require__(moduleId) {
/******/
/******/ 		// Check if module is in cache
/******/ 		if(installedModules[moduleId]) {
/******/ 			return installedModules[moduleId].exports;
/******/ 		}
/******/ 		// Create a new module (and put it into the cache)
/******/ 		var module = installedModules[moduleId] = {
/******/ 			i: moduleId,
/******/ 			l: false,
/******/ 			exports: {}
/******/ 		};
/******/
/******/ 		// Execute the module function
/******/ 		modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);
/******/
/******/ 		// Flag the module as loaded
/******/ 		module.l = true;
/******/
/******/ 		// Return the exports of the module
/******/ 		return module.exports;
/******/ 	}
/******/
/******/
/******/ 	// expose the modules object (__webpack_modules__)
/******/ 	__webpack_require__.m = modules;
/******/
/******/ 	// expose the module cache
/******/ 	__webpack_require__.c = installedModules;
/******/
/******/ 	// define getter function for harmony exports
/******/ 	__webpack_require__.d = function(exports, name, getter) {
/******/ 		if(!__webpack_require__.o(exports, name)) {
/******/ 			Object.defineProperty(exports, name, {
/******/ 				configurable: false,
/******/ 				enumerable: true,
/******/ 				get: getter
/******/ 			});
/******/ 		}
/******/ 	};
/******/
/******/ 	// define __esModule on exports
/******/ 	__webpack_require__.r = function(exports) {
/******/ 		Object.defineProperty(exports, '__esModule', { value: true });
/******/ 	};
/******/
/******/ 	// getDefaultExport function for compatibility with non-harmony modules
/******/ 	__webpack_require__.n = function(module) {
/******/ 		var getter = module && module.__esModule ?
/******/ 			function getDefault() { return module['default']; } :
/******/ 			function getModuleExports() { return module; };
/******/ 		__webpack_require__.d(getter, 'a', getter);
/******/ 		return getter;
/******/ 	};
/******/
/******/ 	// Object.prototype.hasOwnProperty.call
/******/ 	__webpack_require__.o = function(object, property) { return Object.prototype.hasOwnProperty.call(object, property); };
/******/
/******/ 	// __webpack_public_path__
/******/ 	__webpack_require__.p = "";
/******/
/******/
/******/ 	// Load entry module and return exports
/******/ 	return __webpack_require__(__webpack_require__.s = "./src/main.ts");
/******/ })
/************************************************************************/
/******/ ({

/***/ "./src/main.ts":
/*!*********************!*\
  !*** ./src/main.ts ***!
  \*********************/
/*! no exports provided */
/***/ (function(module, __webpack_exports__, __webpack_require__) {

"use strict";
eval("__webpack_require__.r(__webpack_exports__);\n/* harmony import */ var _models_clip__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__(/*! ./models/clip */ \"./src/models/clip.ts\");\n\r\nvar clip = new _models_clip__WEBPACK_IMPORTED_MODULE_0__[\"Clip\"]('Webpack.movie');\r\n\n\n//# sourceURL=webpack:///./src/main.ts?");

/***/ }),

/***/ "./src/models/clip.ts":
/*!****************************!*\
  !*** ./src/models/clip.ts ***!
  \****************************/
/*! exports provided: Clip */
/***/ (function(module, __webpack_exports__, __webpack_require__) {

"use strict";
eval("__webpack_require__.r(__webpack_exports__);\n/* harmony export (binding) */ __webpack_require__.d(__webpack_exports__, \"Clip\", function() { return Clip; });\nvar Clip = (function () {\r\n    function Clip(toTime) {\r\n        this.toTime = toTime;\r\n    }\r\n    return Clip;\r\n}());\r\n\r\n\n\n//# sourceURL=webpack:///./src/models/clip.ts?");

/***/ })

/******/ });

{% endhighlight %}

So what is this, and where is my code? Lets have a look at what this code does.
Simplified it does the following:

{% highlight javascript %}

(function(modules) {
    // The module cache
    var installedModules = {};

    function __webpack_require__(moduleId) {
        // load module with id moduleId
    }

    // some global initialization

    // run entry module
    return __webpack_require__(__webpack_require__.s = "./src/main.ts");

})({
    // 'module 1'
    "./src/main.ts": function () { /* module 1 code  */ },

    // 'module 2'
    "./src/models/clip.ts": function () { /* module 2 code */ }

    // ...
    // 'module n'
})

{% endhighlight %}

When bundle.js was loaded, the function `function(modules)` gets executed and initializes webpack, which basically means it sets up the `__webpack_require__` function.
This function is the core when it comes to resolving modules by a module id. A module id is just the path to the filepath of the file containing the module.
Then webpacks does

{% highlight javascript %}

return __webpack_require__(__webpack_require__.s = "./src/main.ts"); 

{% endhighlight %}

We recognize that this load the file we specified as out entry module in `webpack.config.js`!
How is this done? Lets look at the `__webpack_require__` function:

{% highlight javascript %}

function __webpack_require__(moduleId) {

	// Check if module is in cache
	if(installedModules[moduleId]) {
		return installedModules[moduleId].exports;
	}

	// Create a new module (and put it into the cache)
	var module = installedModules[moduleId] = {
		i: moduleId,
		l: false,
		exports: {}
	};

	// Execute the module function
	modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);

	// Flag the module as loaded
	module.l = true;

	// Return the exports of the module
	return module.exports;
}

{% endhighlight %}

This function checks if the module was already loaded. Was it? No.

{% highlight javascript %}

if(installedModules[moduleId]) {
    return installedModules[moduleId].exports;
}

{% endhighlight %}

Then the function creates a new entry in the module cache and initializes it:

{% highlight javascript %}

var module = installedModules[moduleId] = {
    i: moduleId, // filepath
    l: false,
    exports: {} // remember this as 'the exports of the module'
};

{% endhighlight %}

Then webpack executes the module, but wait what is a 'module' in this context? It is just an entry int the dictionary which was passed to the startup function we are currently inspecting. An we are using the entry with key './src/main.ts'. And what are we doing with this entry? We are executing it:

{% highlight javascript %}

modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);

{% endhighlight %}

`modules[moduleId].call(thisArg, arg1, ...)` is the same as `modules[moduleId].apply(thisArg, [arg1, ...])` and runs a function - just in the case you are wondering what `.call()` does.

So what does the module function look like?


{% highlight javascript %}

function(module, __webpack_exports__, __webpack_require__) {

"use strict";
eval("__webpack_require__.r(__webpack_exports__);\n/* harmony import */ var _models_clip__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__(/*! ./models/clip */ \"./src/models/clip.ts\");\n\r\nvar clip = new _models_clip__WEBPACK_IMPORTED_MODULE_0__[\"Clip\"]('Webpack.movie');\r\n\n\n//# sourceURL=webpack:///./src/main.ts?");

/***/ }
{% endhighlight %}

Well okay lets unwrap the `eval`:

{% highlight javascript %}

__webpack_require__.r(__webpack_exports__);

/* harmony import */
var _models_clip__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__("./src/models/clip.ts");
var clip = new _models_clip__WEBPACK_IMPORTED_MODULE_0__["Clip"]('Webpack.movie');

{% endhighlight %}

Much better! It requires the clip module and creates a new clip object.

`__webpack_require__`: the `__webpack_require__` function from above

`__webpack_exports__`: this i named 'the exports of the module' (see above)

Lets analyze this. What does this code do? At first we have this:

{% highlight javascript %}

__webpack_require__.r(__webpack_exports__);

{% endhighlight %}

It executes the `r` function on the __webpack_require__ function ... object well however you name it in javascript. This `r` function was initialized in the block I named 'some global initialization'. This is the `r` function:

{% highlight javascript %}

function(exports) {
    Object.defineProperty(exports, '__esModule', { value: true });
}

{% endhighlight %}

Okay this only creates a new property `__esModule={value: true}` on 'the exports of the module'.
Back to out module code. The next statement loads the clip module. Finally!!

{% highlight javascript %}

var _models_clip__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__("./src/models/clip.ts");

{% endhighlight %}
 
So loading a module means assiging the return value of `__webpack_require__` to a variable created by webpack. But what is the return value? If we have a look at `__webpack_require__` it returns 'the exports of the module' and as we already know before returning `__webpack_require__` calls the module code, this time of the code of the clip module, and inside the clip module 'the exports of the module' is `__webpack_exports__`.

Here is the clip module (already eval unwrapped):

{% highlight javascript %}

__webpack_require__.r(__webpack_exports__);

/* harmony export (binding) */
__webpack_require__.d(__webpack_exports__, "Clip", function() { return Clip; });

// the transpiled typescript code of the clip class
var Clip =  (function () {
                function Clip(description) {
                    this.description = description;
                }

                return Clip;
            }());

{% endhighlight %}

We know `__webpack_require__.r(__webpack_exports__);` already.

But we dont know `__webpack_require__.d(__webpack_exports__, "Clip", function() { return Clip; });`. Uff what is this again:

{% highlight javascript %}

__webpack_require__.d = function(exports, name, getter) {
	if(!__webpack_require__.o(exports, name)) {
		Object.defineProperty(exports, name, {
			configurable: false,
			enumerable: true,
			get: getter
		});
	}
};

__webpack_require__.o = function(object, property) {
    return Object.prototype.hasOwnProperty.call(object, property);
};

{% endhighlight %}

At first a quick look at `__webpack_require__.o = function(object, property)`, okay this checks if `object` has a property `property`.

Back to `__webpack_require__.d`. Ah okay it basically just creates a property with name `name` on 'the exports of the module' if there is not already a property with the same name. The getter of the property is the passed `getter` function. Fine, back to the clips module:

{% highlight javascript %}

__webpack_require__.d(__webpack_exports__, "Clip", function() { return Clip; });

{% endhighlight %}

As we know `__webpack_require__.d` just creates a new property 'Clip' and the getter returns the `Clip` constructor.

So 'the exports of the module' contains a property which is the clip constructor. EASY!

Back to the main module:

{% highlight javascript %}

__webpack_require__.r(__webpack_exports__);

/* harmony import */
var _models_clip__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__("./src/models/clip.ts");
var clip = new _models_clip__WEBPACK_IMPORTED_MODULE_0__["Clip"]('Webpack.movie');

{% endhighlight %}

We said that `__webpack_require__` returns 'the exports of the module' and 'the exports of the module'["Clip"] means the `Clip` constructor.

Thats all the magic!


## Conclusion

We showed how webpack bundles several typescript files, they are just 'modules' and a 'module' is an entry inside the modules dictionary with its filepath as key. The value 'module' entry is a functions which sets properties on 'the exports of the module' which then can be accessed by other 'modules' using the `__webpack_require__` function.

## Fun Fact

If you review the whole bundle.js file there is absolutely no code which handles the cases when a module was not found. But why? Simply because webpack knows that will never ever happen!

Thank you for reading!
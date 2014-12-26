
pegjs-otf
=========

On-The-Fly Compilation of [PEG.js](http://pegjs.org/) grammars for Node and especially for Browser.

<p/>
<img src="https://nodei.co/npm/pegjs-otf.png?downloads=true&stars=true" alt=""/>

<p/>
<img src="https://david-dm.org/rse/pegjs-otf.png" alt=""/>

About
-----

This is a small wrapper class around the [PEG.js](http://pegjs.org/) API and a companion
[Browserify](http://browserify.org/) transform for on-the-fly (OTF) compiling PEG.js grammars into
parser code.

Installation
------------

```shell
$ npm install pegjs-otf
$ npm install browserify
```

Usage
-----

With a sample grammar `sample.pegjs` like this...

```
sample
    = _ "hello" _ who:[A-Za-z]+ _ { return who.join(""); }

_ "blank"
    = (co / ws)*

co "comment"
    = "//" (![\r\n] .)*
    / "/*" (!"*/" .)* "*/"

ws "whitespaces"
    = [ \t\r\n]+
```

...instead of using a `sample.js` driver...

```js
var PEG = require("pegjs-otf");
var fs = require("fs");
var parser = PEG.buildParser(
    fs.readFileSync(__dirname + "/sample.pegjs", "utf8"),
    { optimize: "size" }
);
console.log(parser.parse("hello world") === "world" ? "OK" : "FAIL");
```

...now use a `sample.js` driver like this:

```js
var PEG = require("pegjs-otf");
var parser = PEG.buildParserFromFile(
    __dirname + "/sample.pegjs",
    { optimize: "size" }
);
console.log(parser.parse("hello world") === "world" ? "OK" : "FAIL");
```

Then it will work in both Node (through on-the-fly run-time compilation)...

```shell
$ node sample.js
OK
```

...and the Browser (with the help of Browserify through on-the-fly compile-time compilation):

```shell
$ browserify -t pegjs-otf/transform -o sample.browser.js sample.js
$ node sample.browser.js
OK
```

How It Works
------------

The API returned by `require("pegjs-otf")` is really just the PEG.js API
with an additional method `buildParserFromFile`.
And this `buildParserFromFile(filename, options)` is actually not really some sort of a convenience function,
because it technically really is just `require("pegjs").buildParser(require("fs").readFileSync(filename), options)`
and this would not warrant an extra wrapper API, of course. Instead
the `pegjs-otf` module and its `buildParserFromFile` is actually a *marker*.
In a regular Node environment it really just performs its
`require("pegjs").buildParser(require("fs").readFileSync(filename), options)` operation.
But when the application code is transpiled for Browser usage with the help
of Browserify, then the `pegjs-otf/transform` transform kicks in and
replaces the `require("pegjs-otf")` call with nothing and the
`buildParserFromFile(filename, options)` call with the corresponding parser code(!).

License
-------

Copyright (c) 2014 Ralf S. Engelschall (http://engelschall.com/)

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be included
in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.


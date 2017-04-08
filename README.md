# api documentation for  [copy-paste (v1.3.0)](https://github.com/xavi-/node-copy-paste)  [![npm package](https://img.shields.io/npm/v/npmdoc-copy-paste.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-copy-paste) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-copy-paste.svg)](https://travis-ci.org/npmdoc/node-npmdoc-copy-paste)
#### A command line utility that allows read/write (i.e copy/paste) access to the system clipboard.

[![NPM](https://nodei.co/npm/copy-paste.png?downloads=true)](https://www.npmjs.com/package/copy-paste)

[![apidoc](https://npmdoc.github.io/node-npmdoc-copy-paste/build/screenCapture.buildApidoc.browser.%252Fhome%252Ftravis%252Fbuild%252Fnpmdoc%252Fnode-npmdoc-copy-paste%252Ftmp%252Fbuild%252Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-copy-paste/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-copy-paste/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-copy-paste/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "bugs": {
        "url": "https://github.com/xavi-/node-copy-paste/issues"
    },
    "dependencies": {
        "iconv-lite": "^0.4.8",
        "sync-exec": "~0.6.x"
    },
    "description": "A command line utility that allows read/write (i.e copy/paste) access to the system clipboard.",
    "devDependencies": {
        "mocha": "*",
        "should": ">=8.2.1"
    },
    "directories": {},
    "dist": {
        "shasum": "a7e6c4a1c28fdedf2b081e72b97df2ef95f471ed",
        "tarball": "https://registry.npmjs.org/copy-paste/-/copy-paste-1.3.0.tgz"
    },
    "gitHead": "160fae2bd4352739f26459dc965c9df0c5c7038b",
    "homepage": "https://github.com/xavi-/node-copy-paste",
    "keywords": [
        "copy",
        "paste",
        "copy and paste",
        "clipboard"
    ],
    "main": "./index.js",
    "maintainers": [
        {
            "name": "xavi",
            "email": "xavi.rmz@gmail.com"
        }
    ],
    "name": "copy-paste",
    "optionalDependencies": {
        "sync-exec": "~0.6.x"
    },
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/xavi-/node-copy-paste.git"
    },
    "scripts": {
        "test": "mocha -w"
    },
    "version": "1.3.0"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module copy-paste](#apidoc.module.copy-paste)
1.  [function <span class="apidocSignatureSpan">copy-paste.</span>copy (text, callback)](#apidoc.element.copy-paste.copy)
1.  [function <span class="apidocSignatureSpan">copy-paste.</span>global ()](#apidoc.element.copy-paste.global)
1.  [function <span class="apidocSignatureSpan">copy-paste.</span>noConflict ()](#apidoc.element.copy-paste.noConflict)
1.  [function <span class="apidocSignatureSpan">copy-paste.</span>paste (callback)](#apidoc.element.copy-paste.paste)
1.  [function <span class="apidocSignatureSpan">copy-paste.</span>silent ()](#apidoc.element.copy-paste.silent)
1.  object <span class="apidocSignatureSpan">copy-paste.</span>darwin
1.  object <span class="apidocSignatureSpan">copy-paste.</span>linux
1.  object <span class="apidocSignatureSpan">copy-paste.</span>win32

#### [module copy-paste.darwin](#apidoc.module.copy-paste.darwin)
1.  [function <span class="apidocSignatureSpan">copy-paste.darwin.</span>decode (chunks)](#apidoc.element.copy-paste.darwin.decode)
1.  [function <span class="apidocSignatureSpan">copy-paste.darwin.</span>encode (str)](#apidoc.element.copy-paste.darwin.encode)
1.  object <span class="apidocSignatureSpan">copy-paste.darwin.</span>copy
1.  object <span class="apidocSignatureSpan">copy-paste.darwin.</span>paste

#### [module copy-paste.linux](#apidoc.module.copy-paste.linux)
1.  [function <span class="apidocSignatureSpan">copy-paste.linux.</span>decode (chunks)](#apidoc.element.copy-paste.linux.decode)
1.  [function <span class="apidocSignatureSpan">copy-paste.linux.</span>encode (str)](#apidoc.element.copy-paste.linux.encode)
1.  object <span class="apidocSignatureSpan">copy-paste.linux.</span>copy
1.  object <span class="apidocSignatureSpan">copy-paste.linux.</span>paste

#### [module copy-paste.win32](#apidoc.module.copy-paste.win32)
1.  [function <span class="apidocSignatureSpan">copy-paste.win32.</span>decode (chunks)](#apidoc.element.copy-paste.win32.decode)
1.  [function <span class="apidocSignatureSpan">copy-paste.win32.</span>encode (str)](#apidoc.element.copy-paste.win32.encode)
1.  object <span class="apidocSignatureSpan">copy-paste.win32.</span>copy
1.  object <span class="apidocSignatureSpan">copy-paste.win32.</span>paste



# <a name="apidoc.module.copy-paste"></a>[module copy-paste](#apidoc.module.copy-paste)

#### <a name="apidoc.element.copy-paste.copy"></a>[function <span class="apidocSignatureSpan">copy-paste.</span>copy (text, callback)](#apidoc.element.copy-paste.copy)
- description and source-code
```javascript
copy = function (text, callback) {
	var child = spawn(config.copy.command, config.copy.args);

	var done = (callback ? function() { callback.apply(this, arguments); done = noop; } : noop);

	var err = [];

	child.stdin.on("error", function (err) { done(err); });
	child
		.on("exit", function() { done(null, text); })
		.on("error", function(err) { done(err); })
		.stderr
			.on("data", function(chunk) { err.push(chunk); })
			.on("end", function() {
				if(err.length === 0) { return; }
				done(new Error(config.decode(err)));
			})
	;

	if(text.pipe) { text.pipe(child.stdin); }
	else {
		var output, type = Object.prototype.toString.call(text);

		if(type === "[object String]") { output = text; }
		else if(type === "[object Object]") { output = util.inspect(text, { depth: null }); }
		else if(type === "[object Array]") { output = util.inspect(text, { depth: null }); }
		else { output = text.toString(); }

		child.stdin.end(config.encode(output));
	}

	return text;
}
```
- example usage
```shell
...
- 'require("copy-paste").global()':  adds 'copy' and 'paste' to the global namespace.  Returns an object with 'copy' and 'paste'
as properties.

## Example

'''js
var ncp = require("copy-paste");

ncp.copy('some text', function () {
  // complete...
})
'''

## Getting node-copy-paste

The easiest way to get node-copy-paste is with [npm](http://npmjs.org/):
...
```

#### <a name="apidoc.element.copy-paste.global"></a>[function <span class="apidocSignatureSpan">copy-paste.</span>global ()](#apidoc.element.copy-paste.global)
- description and source-code
```javascript
global = function () {
	GLOBAL.copy = exports.copy;
	GLOBAL.paste = exports.paste;

	return exports;
}
```
- example usage
```shell
...
When 'require("copy-paste")' is executed, an object with the following properties is returned:

- 'copy(text[, callback])': asynchronously replaces the current contents of the clip board with 'text'.  Takes either a string,
array, object, or readable stream.  Returns the same value passed in. Optional callback will fire when the copy operation is complete
.
- 'paste([callback])': if no callback is provided, 'paste' synchronously returns the current contents of the system clip board.
Otherwise, the contents of the system clip board are passed to the callback as the second parameter.

	**Note**: The synchronous version of 'paste' is not always availabled.  Unfortunately, I'm having a hard time finding a synchronous
 version of 'child_process.exec' that consistently works on all platforms, especially windows.  An error message is shown if the
 synchronous version of 'paste' is used on an unsupported platform.  That said, the asynchronous version of 'paste' is always available
.

- 'require("copy-paste").global()':  adds 'copy' and 'paste' to the global namespace.  Returns an object with 'copy' and 'paste'
as properties.

## Example

'''js
var ncp = require("copy-paste");

ncp.copy('some text', function () {
...
```

#### <a name="apidoc.element.copy-paste.noConflict"></a>[function <span class="apidocSignatureSpan">copy-paste.</span>noConflict ()](#apidoc.element.copy-paste.noConflict)
- description and source-code
```javascript
noConflict = function () {
	throw new Error("DEPRECATED: copy-paste no longer adds global variables by default.");
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.copy-paste.paste"></a>[function <span class="apidocSignatureSpan">copy-paste.</span>paste (callback)](#apidoc.element.copy-paste.paste)
- description and source-code
```javascript
paste = function (callback) {
	if(execSync && !callback) { return config.decode(execSync(pasteCommand)); }
	else if(callback) {
		var child = spawn(config.paste.command, config.paste.args);

		var done = callback && function() { callback.apply(this, arguments); done = noop; };

		var data = [], err = [];

		child.on("error", function(err) { done(err); });
		child.stdout
			.on("data", function(chunk) { data.push(chunk); })
			.on("end", function() { done(null, config.decode(data)); })
		;
		child.stderr
			.on("data", function(chunk) { err.push(chunk); })
			.on("end", function() {
				if(err.length === 0) { return; }

				done(new Error(config.decode(err)));
			})
		;
	} else {
		throw new Error("A synchronous version of paste is not supported on this platform.");
	}
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.copy-paste.silent"></a>[function <span class="apidocSignatureSpan">copy-paste.</span>silent ()](#apidoc.element.copy-paste.silent)
- description and source-code
```javascript
silent = function () {
	throw new Error("DEPRECATED: copy-paste is now always silent.");
}
```
- example usage
```shell
n/a
```



# <a name="apidoc.module.copy-paste.darwin"></a>[module copy-paste.darwin](#apidoc.module.copy-paste.darwin)

#### <a name="apidoc.element.copy-paste.darwin.decode"></a>[function <span class="apidocSignatureSpan">copy-paste.darwin.</span>decode (chunks)](#apidoc.element.copy-paste.darwin.decode)
- description and source-code
```javascript
decode = function (chunks) {
	if(!Array.isArray(chunks)) { chunks = [ chunks ]; }

	return Buffer.concat(chunks).toString("utf8");
}
```
- example usage
```shell
...
	child
		.on("exit", function() { done(null, text); })
		.on("error", function(err) { done(err); })
		.stderr
			.on("data", function(chunk) { err.push(chunk); })
			.on("end", function() {
				if(err.length === 0) { return; }
				done(new Error(config.decode(err)));
			})
	;

	if(text.pipe) { text.pipe(child.stdin); }
	else {
		var output, type = Object.prototype.toString.call(text);
...
```

#### <a name="apidoc.element.copy-paste.darwin.encode"></a>[function <span class="apidocSignatureSpan">copy-paste.darwin.</span>encode (str)](#apidoc.element.copy-paste.darwin.encode)
- description and source-code
```javascript
encode = function (str) { return new Buffer(str, "utf8"); }
```
- example usage
```shell
...
		var output, type = Object.prototype.toString.call(text);

		if(type === "[object String]") { output = text; }
		else if(type === "[object Object]") { output = util.inspect(text, { depth: null }); }
		else if(type === "[object Array]") { output = util.inspect(text, { depth: null }); }
		else { output = text.toString(); }

		child.stdin.end(config.encode(output));
	}

	return text;
};

var pasteCommand = [ config.paste.command ].concat(config.paste.args).join(" ");
exports.paste = function(callback) {
...
```



# <a name="apidoc.module.copy-paste.linux"></a>[module copy-paste.linux](#apidoc.module.copy-paste.linux)

#### <a name="apidoc.element.copy-paste.linux.decode"></a>[function <span class="apidocSignatureSpan">copy-paste.linux.</span>decode (chunks)](#apidoc.element.copy-paste.linux.decode)
- description and source-code
```javascript
decode = function (chunks) {
	if(!Array.isArray(chunks)) { chunks = [ chunks ]; }

	return Buffer.concat(chunks).toString("utf8");
}
```
- example usage
```shell
...
	child
		.on("exit", function() { done(null, text); })
		.on("error", function(err) { done(err); })
		.stderr
			.on("data", function(chunk) { err.push(chunk); })
			.on("end", function() {
				if(err.length === 0) { return; }
				done(new Error(config.decode(err)));
			})
	;

	if(text.pipe) { text.pipe(child.stdin); }
	else {
		var output, type = Object.prototype.toString.call(text);
...
```

#### <a name="apidoc.element.copy-paste.linux.encode"></a>[function <span class="apidocSignatureSpan">copy-paste.linux.</span>encode (str)](#apidoc.element.copy-paste.linux.encode)
- description and source-code
```javascript
encode = function (str) { return new Buffer(str, "utf8"); }
```
- example usage
```shell
...
		var output, type = Object.prototype.toString.call(text);

		if(type === "[object String]") { output = text; }
		else if(type === "[object Object]") { output = util.inspect(text, { depth: null }); }
		else if(type === "[object Array]") { output = util.inspect(text, { depth: null }); }
		else { output = text.toString(); }

		child.stdin.end(config.encode(output));
	}

	return text;
};

var pasteCommand = [ config.paste.command ].concat(config.paste.args).join(" ");
exports.paste = function(callback) {
...
```



# <a name="apidoc.module.copy-paste.win32"></a>[module copy-paste.win32](#apidoc.module.copy-paste.win32)

#### <a name="apidoc.element.copy-paste.win32.decode"></a>[function <span class="apidocSignatureSpan">copy-paste.win32.</span>decode (chunks)](#apidoc.element.copy-paste.win32.decode)
- description and source-code
```javascript
decode = function (chunks) {
	if(!Array.isArray(chunks)) { chunks = [ chunks ]; }

	var b64 = iconv.decode(Buffer.concat(chunks), "cp437");
	b64 = b64.substr(0, b64.length - 2); // Chops off extra "\r\n"

    // remove bom and decode
    var result = new Buffer(b64, "base64").slice(3).toString("utf-8");
    return result;
}
```
- example usage
```shell
...
	child
		.on("exit", function() { done(null, text); })
		.on("error", function(err) { done(err); })
		.stderr
			.on("data", function(chunk) { err.push(chunk); })
			.on("end", function() {
				if(err.length === 0) { return; }
				done(new Error(config.decode(err)));
			})
	;

	if(text.pipe) { text.pipe(child.stdin); }
	else {
		var output, type = Object.prototype.toString.call(text);
...
```

#### <a name="apidoc.element.copy-paste.win32.encode"></a>[function <span class="apidocSignatureSpan">copy-paste.win32.</span>encode (str)](#apidoc.element.copy-paste.win32.encode)
- description and source-code
```javascript
encode = function (str) { return iconv.encode(str, "utf16le"); }
```
- example usage
```shell
...
		var output, type = Object.prototype.toString.call(text);

		if(type === "[object String]") { output = text; }
		else if(type === "[object Object]") { output = util.inspect(text, { depth: null }); }
		else if(type === "[object Array]") { output = util.inspect(text, { depth: null }); }
		else { output = text.toString(); }

		child.stdin.end(config.encode(output));
	}

	return text;
};

var pasteCommand = [ config.paste.command ].concat(config.paste.args).join(" ");
exports.paste = function(callback) {
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)

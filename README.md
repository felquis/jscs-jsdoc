# jscs-jsdoc
[![Gitter](https://img.shields.io/badge/GITTER-JOIN_CHAT_%E2%86%92-1dce73.svg)](https://gitter.im/jscs-dev/talks?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Build Status](https://img.shields.io/travis/jscs-dev/jscs-jsdoc.svg)](http://travis-ci.org/jscs-dev/jscs-jsdoc?style=flat)
[![Dependency Status](https://img.shields.io/david/jscs-dev/jscs-jsdoc.svg)](https://david-dm.org/jscs-dev/jscs-jsdoc)
[![Coverage](https://img.shields.io/coveralls/jscs-dev/jscs-jsdoc.svg)](https://coveralls.io/r/jscs-dev/jscs-jsdoc)

[![NPM version](https://img.shields.io/npm/v/jscs-jsdoc.svg)](https://www.npmjs.com/package/jscs-jsdoc)
[![NPM downloads](https://img.shields.io/npm/dm/jscs-jsdoc.svg)](https://www.npmjs.com/package/jscs-jsdoc)
[![MIT License](https://img.shields.io/npm/l/jscs-jsdoc.svg)](https://github.com/jscs-dev/jscs-jsdoc/blob/master/LICENSE)

`jsdoc` plugin for [jscs](https://github.com/jscs-dev/node-jscs/). [Twitter](https://twitter.com/jscs_dev) | [Mailing List](https://groups.google.com/group/jscs-dev)

## Table of Contents

- [Installation](#plugin-installation)
- [Versioning & Semver](#versioning--semver)
- [Usage](#usage)
- [Rules](#rules)
- [Browser Usage](#browser-usage)

## Plugin installation

`jscs-jsdoc` can be installed using NPM and requires [jscs](https://github.com/jscs-dev/node-jscs/#installation).

Install it globally if you are using globally installed `jscs`

    npm -g install jscs-jsdoc

But better install it into your project

    npm install jscs-jsdoc --save-dev

## Versioning & Semver

We recommend installing `jscs-jsdoc` via NPM using `^`, or `~` if you want more stable releases.

Semver (http://semver.org/) dictates that breaking changes be major version bumps. In the context of a linting tool, a bug fix that causes more errors to be reported can be interpreted as a breaking change. However, that would require major version bumps to occur more often than can be desirable. Therefore, as a compromise, we will only release bug fixes that cause more errors to be reported in minor versions.

Below you fill find our versioning strategy, and what you can expect to come out of a new `jscs-jsdoc` release.

 * Patch release:
   * A bug fix in a rule that causes `jscs-jsdoc` to report less errors;
   * Docs, refactoring and other "invisible" changes for user;
 * Minor release:
   * Any preset changes;
   * A bug fix in a rule that causes `jscs-jsdoc` to report more errors;
   * New rules or new options for existing rules that don't change existing behavior;
   * Modifying rules so they report less errors, and don't cause build failures;
 * Major release:
   * Purposefully modifying existing rules so that they report more errors or change the meaning of a rule;
   * Any architectural changes that could cause builds to fail.

## Usage

To use plugin you should add these lines to configuration file `.jscsrc`:

```json
{
    "plugins": [
        "jscs-jsdoc"
    ],
    "jsDoc": {
        "checkAnnotations": "closurecompiler",
        "checkTypes": "strictNativeCase",
        "enforceExistence": "exceptExports"
    }
}
```

## Rules

### checkAnnotations

Ensures tag names are valid

There are 3 presets for `Closure Compiler`, `JSDoc3` and `JSDuck5`.

By default it allows any tag of mixed set. You can pass `Object` to select preset with `preset` field and add custom tags with `extra` field.

Type: `Boolean` or `String` or `{"preset": String, "extra": Object}` (see [tag values](#user-content-tag-values))

Values: `true`, `"closurecompiler"`, `"jsdoc3"`, `"jsduck5"`, `Object`

Context: `file`

Tags: `*`

#### Tag values

`extra` field should contains tags in keys and there are options for values:
- `false` means tag available with no value
- `true` means tag available with any value
- `"some"` means tag available and requires some value

See also [tag presets](https://github.com/jscs-dev/jscs-jsdoc/tree/master/lib/tags).

#### Example

```js
"checkAnnotations": true
```

##### Valid

```js
/**
 * @chainable
 * @param {string} message
 * @return {string}
 */
function _f() {}
```

##### Invalid

```js
/**
 * @pororo
 * @lalala
 */
function _f() {}
```

#### Example 2

```js
"checkAnnotations": {
    "preset": "jsdoc3",
    "extra": {
        "boomer": false
    }
}
```

##### Valid

```js
/**
 * @boomer
 * @argument {String}
 */
function _f() {}
```

##### Invalid

```js
/** @still-invalid */
```

####

### checkParamNames

Ensures param names in jsdoc and in function declaration are equal

Type: `Boolean`

Values: `true`

Context: `functions`

Tags: `param`, `arg`, `argument`

#### Example

```js
"checkParamNames": true
```

##### Valid

```js
/**
 * @param {String} message
 * @param {Number|Object} [line]
 */
function method(message, line) {}
```

##### Invalid

```js
/**
 * @param {String} msg
 * @param {Number|Object} [line]
 */
function method(message) {}
```

### requireParamTypes

Ensures params in jsdoc contains type

Type: `Boolean`

Values: `true`

Context: `functions`

Tags: `param`, `arg`, `argument`

#### Example

```js
"requireParamTypes": true
```

##### Valid

```js
/**
 * @param {String} message
 */
function method() {}
```

##### Invalid

```js
/**
 * @param message
 */
function method() {}
```

### checkRedundantParams

Reports redundant params in jsdoc

Type: `Boolean`

Values: `true`

Context: `functions`

Tags: `param`, `arg`, `argument`

#### Example

```js
"checkRedundantParams": true
```

##### Valid

```js
/**
 * @param {String} message
 */
function method(message) {}
```

##### Invalid

```js
/**
 * @param {String} message
 */
function method() {}
```

### checkReturnTypes

Reports discrepancies between the claimed in jsdoc and actual type if both exist (code scan)

Type: `Boolean`

Values: `true`

Context: `functions`

Tags: `return`, `returns`

#### Example

```js
"checkReturnTypes": true
```

##### Valid

```js
/**
 * @returns {String}
 */
function method() {
    return 'foo';
}
```

##### Invalid

```js
/**
 * @returns {String}
 */
function method(f) {
    if (f) {
        return true;
    }
    return 1;
}
```

### checkRedundantReturns

Report statements for functions with no return

Type: `Boolean`

Values: `true`

Context: `functions`

Tags: `return`, `returns`

#### Example

```js
"checkRedundantReturns": true
```

##### Valid

```js
/**
 * @returns {string}
 */
function f() {
    return 'yes';
}
```

##### Invalid

```js
/**
 * @returns {string}
 */
function f() {
    // no return here
}
```

### requireReturnTypes

Ensures returns in jsdoc contains type

Type: `Boolean`

Values: `true`

Context: `functions`

Tags: `return`, `returns`

#### Example

```js
"requireReturnTypes": true
```

##### Valid

```js
/**
 * @returns {String}
 */
function method() {}

/**
 * no @return
 */
function method() {}
```

##### Invalid

```js
/**
 * @returns
 */
function method() {}
```

### checkTypes

Reports invalid types for bunch of tags

In `strictNativeCase` mode ensures that case of natives is the same as in this list: `boolean`, `number`, `string`, `Object`, `Array`, `Date`, `RegExp`.

In `capitalizedNativeCase` mode ensures that first letter in all native types and primitives is uppercased except the case with `function` in google closure format: `{function(...)}`

Type: `Boolean` or `String`

Values: `true` or `"strictNativeCase"` or `"capitalizedNativeCase"`

Context: `*`

Tags: `typedef`, `type`, `param`, `return`, `returns`, `enum`, `var`, `prop`, `property`, `arg`, `argument`, `cfg`, `lends`, `extends`, `implements`, `define`

#### Example

```js
"checkTypes": true
```

##### Valid

```js
/**
 * @typedef {Object} ObjectLike
 * @property {boolean} hasFlag
 * @property {string} name
 */

/** @type {number} */
var bar = 1;

/** @const {number} */
var FOO = 2;

/**
 * @const
 * @type {number}
 */
var BAZ = 3;

/**
 * @param {SomeX} x
 * @returns {string}
 */
function method(x) {}
```

##### Invalid

```js
/** @type {some~number} */
var x = 1;

/**
 * @param {function(redundantName: Number)} x
 */
function method(x) {}

/**
 * @param {Number|Boolean|object|array} x invalid for strictNativeCase
 */
function method(x) {}
```

```js
/** @type {some~number} */
var x = 1;
```

### checkRedundantAccess

Reports redundant access declarations

Type: `Boolean` or `String`

Values: `true` or `"enforceLeadingUnderscore"` or `"enforceTrailingUnderscore"`

Context: `functions`

Tags: `access`, `private`, `protected`, `public`

#### Example

```js
"checkRedundantAccess": true
"checkRedundantAccess": "enforceLeadingUnderscore"
```

##### Valid for true, "enforceLeadingUnderscore"

```js
/**
 * @access private
 */
function _f() {}

/**
 * @access public
 */
function f() {}
```

##### Invalid for true

```js
/**
 * @private
 * @access private
 */
function _f() {}
```

##### Invalid for "enforceLeadingUnderscore"

```js
/**
 * @private
 */
function _f() {}
```

### leadingUnderscoreAccess

Ensures access declaration is set for `_underscored` function names

Ignores a bunch of popular identifiers: `__filename`, `__dirname`, `__proto__`, `__defineGetter__`, `super_`, `__constructor`, etc.

Type: `Boolean` or `String`

Values: `true` (means not public), `"private"`, `"protected"`

Context: `functions`

Tags: `access`, `private`, `protected`, `public`

#### Example

```js
"leadingUnderscoreAccess": "protected"
```

##### Valid

```js
/**
 * @protected
 */
function _f() {}
```

##### Invalid

```js
function _g() {}

/**
 * @private
 */
function _e() {}
```

### enforceExistence

Ensures jsdoc block exist

Type: `Boolean` or `String`

Values: `true` or `"exceptExports"` (skip `module.exports = function () {};`)

Context: `functions`

#### Example

```js
"enforceExistence": true
```

##### Valid

```js
/**
 * @protected
 */
function _f() {}
```

##### Invalid

```js
function _g() {}
```


### requireHyphenBeforeDescription

Ensures a param description has a hyphen before it (checks for `- `)

Type: `Boolean`

Values: `true`

Context: `functions`

Tags: `param`, `arg`, `argument`

#### Example

```js
"requireHyphenBeforeDescription": true
```

##### Valid

```js
/**
 * @param {String} - message
 */
function method() {}
```

##### Invalid

```js
/**
 * @param {String} message
 */
function method() {}
```


### requireNewlineAfterDescription

Ensures a doc comment description has padding newline

Type: `Boolean`

Values: `true`

Context: `functions`

Tags: `*`

#### Example

```js
"requireNewlineAfterDescription": true
```

##### Valid

```js
/**
 * @param {String} - message
 */
function method() {}

/**
 * Description
 */
function method() {}

/**
 * Description
 *
 * @param {String} - message
 */
function method() {}
```

##### Invalid

```js
/**
 * Description
 * @param {String} message
 */
function method() {}
```


### disallowNewlineAfterDescription

Ensures a doc comment description has no padding newlines

Type: `Boolean`

Values: `true`

Context: `functions`

Tags: `*`

#### Example

```js
"disallowNewlineAfterDescription": true
```

##### Valid

```js
/**
 * @param {String} - message
 */
function method() {}

/**
 * Description
 */
function method() {}

/**
 * Description
 * @param {String} - message
 */
function method() {}
```

##### Invalid

```js
/**
 * Description
 *
 * @param {String} message
 */
function method() {}
```


### requireDescriptionCompleteSentence

Ensures a doc comment description is a complete sentence.

A complete sentence is defined as starting with an upper case letter and ending with a period.

Type: `Boolean`

Values: `true`

Context: `functions`

Tags: `*`

#### Example

```js
"requireDescriptionCompleteSentence": true
```

##### Valid

```js
/**
 * @param {String} - message
 */
function method() {}

/**
 * Description.
 */
function method() {}

/**
 * Description.
 *
 * @param {String} - message
 */
function method() {}

/**
 * Description
 * On multiple lines.
 *
 * @param {String} - message
 */
function method() {}
```

##### Invalid

```js
/**
 * Description
 * @param {String} message
 */
function method() {}

/**
 * description starting with a lower case letter.
 * @param {String} message
 */
function method() {}

/**
 * Description period is offset .
 * @param {String} message
 */
function method() {}

/**
 * Description!
 * @param {String} message
 */
function method() {}
```


### requireParamDescription

Ensures a param description exists.

Type: `Boolean`

Values: `true`

Context: `functions`

Tags: `param`, `arg`, `argument`

#### Example

```js
"requireParamDescription": true
```

##### Valid

```js
/**
 * @param {String} arg message
 */
function method(arg) {}

/**
 * @param arg message
 */
function method(arg) {}

##### Invalid

```js
/**
 * @param {String} arg
 */
function method(arg) {}

/**
 * @param arg
 */
function method(arg) {}
```


## Browser Usage

NOT SUPPORTED ATM. SORRY.

File [jscs-jsdoc-browser.js](jscs-jsdoc-browser.js) contains browser-compatible version of `jscs-jsdoc`.

Download and include `jscs-jsdoc-browser.js` into your page just after `jscs-browser.js`.

```html
<script src="jscs-browser.js"></script>
<script src="jscs-jsdoc-browser.js"></script>
<script>
    var checker = new JscsStringChecker();
    checker.registerDefaultRules();
    checker.configure({'jsDoc': {/* ... */}});
    var errors = checker.checkString('var x, y = 1;');
    errors.getErrorList().forEach(function (error) {
        console.log(errors.explainError(error));
    });
</script>
```

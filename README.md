# Introduction

`import-js` is a tool to automatically import dependencies in your JavaScript
project. Use it in Vim or Emacs by placing your cursor on a variable and hit
`<leader>j` (Vim), or `(M-x) import-js-import` (Emacs).

![Demo of import-js in action](https://raw.github.com/trotzig/import-js/master/import-js-demo.gif)

## Editor support

import-js comes with plugins for the following editors:

- [Emacs (experimental)](EMACS.md) (Thanks to @kevin.kehl!)
- [Vim](VIM.md)
- [(your editor here?)](CONTRIBUTING.md)

Detailed instructions on how to install import-js can be found in the editor
links above.

*Want to add another editor to the list?* [See how to
contribute](CONTRIBUTING.md).

## Importing: Example

To demonstrate what import-js can do, let's use an example. Let's say that you
have a JavaScript project with the following setup:

```
.
|-- index.html
|-- components
|     |-- button.js
|     |-- icon.js
|-- vendor
|     |--
|-- pages
|     |-- index.js
```

Let's pretend that you're editing `pages/index.js` that currently looks like
this:

```js
document.createElement(new Button({ text: 'Save' }).toDOMElement());
```

At this point, `Button` is undefined. We need to import it. If you are used to
doing this manually, this involves figuring out the path to the JavaScript
module that defines `Button`. With import-js, you instead place your cursor on
the word "Button", then hit `<leader>j` (Vim) or `(M-x) import-js-import`
(Emacs). The file buffer will now change to the following:

```js
var Button = require('components/button');

document.createElement(new Button({ text: 'Save' }).toDOMElement());
```

That's basically it. Import-js will help you find modules and automatically add
a `require` statement. But keep reading for some more neat features.

## Fix imports

If you have [eslint](http://eslint.org/) installed, import-js can be used to
automatically fix all imports. By hiting `<leader>i` (Vim), all your undefined
variables will be resolved, and all your unused imports will be removed. By
default, import-js expects a global `eslint` command to be available.

## Experimental: Go to module

Since import-js is pretty good at finding JS modules, it makes sense that
there's an option to open/go to a file rather than import it. This is similar
to VIM's built in ["Open file under
cursor"](http://vim.wikia.com/wiki/Open_file_under_cursor). Use it by placing
the cursor on a variable and hit `<leader>g` (Vim) or `(M-x) import-js-goto`
(Emacs).

## Things to note

- Only files ending in .js\* are considered when importing
- All imports are expressed on one line each, starting with
  `var`/`const`/`let`/`import` (configurable through the `declaration_keyword`
  option)
- As part of resolving an import, all imports will be sorted
- The Vim plugin is written in Ruby. You need a [Vim with Ruby support](VIM.md).

## Configuration

Create a file called `.importjs.json` in the root folder of your project to
configure import-js. The following configuration options can be used.

### `lookup_paths`

Configure where import-js should look to resolve imports. If you are using
Webpack, these should match the `modulesDirectories` configuration. Example:

```json
"lookup_paths": [
  "app/assets/javascripts",
  "react-components"
]
```

*Tip:* Don't put `node_modules` here. import-js will find your Node
dependencies through your `package.json` file.

### `excludes`

Define a list of glob patterns that match files and directories that you don't
want to include for importing.

```json
"excludes": [
  "react-components/**/test/**"
]
```

### `aliases`

Some variable names might not easily map to a file in the filesystem. For
those, you can add them to the `aliases` configuration.

```json
"aliases": {
  "$": "third-party-libs/jquery",
  "_": "third-party-libs/underscore"
}
```

If you have a library that expose a single object that has a bunch of objects
on it that you want to use, you can list those in a `destructure` array inside
the alias (which then has to be turned into an object):

```json
"aliases": {
  "$": "third-party-libs/jquery",
  "_": {
    "path": "third-party-libs/underscore",
    "destructure": ["memoize", "debounce"]
  }
}
```

Imports then use [ES6 Destructuring Assigment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment),
e.g.

```javascript
const { memoize } = require('underscore');

memoize(() => { foo() });
```

### `declaration_keyword`

If you are using ES6 (ES 2015), you have access to `let`, `const`, and `import`
in addition to `var` as ways to declare variables. To use one of these, set the
`declaration_keyword` configuration.

```json
"declaration_keyword": "const"
```

If you set it to `import`, you get imports of the form `import foo from 'foo'`.

```json
"declaration_keyword": "import"
```

### `strip_file_extensions`

An array that controls what file extensions are stripped out from the resulting
`require` statement. The default configuration strips out `[".js", ".jsx"]`.
Set to an empty array `[]` to avoid stripping out extensions.

```json
"strip_file_extensions": [".web.js", ".js"]
```

## Contributing

See the
[CONTRIBUTING.md](https://github.com/trotzig/import-js/blob/master/CONTRIBUTING.md)
document for tips on how to run, test and develop import-js locally.

Happy hacking!

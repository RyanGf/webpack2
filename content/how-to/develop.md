---
title: 如何开发？
contributors:
  - SpaceK33z
---

On this page we'll explain how to get started with developing and how to choose one of three tools to develop. It is assumed you already have a webpack configuration file.

W> Never use any of these tools in production. Ever.

## Adjusting Your Text Editor

Some text editors have a "safe write" feature and enable this by default. As a result, saving a file will not always result in a recompile.

Each editor has a different way of disabling this. For the most common ones:

* **Sublime Text 3** - Add `"atomic_save": false` to your user preferences.
* **IntelliJ** - use search in the preferences to find "safe write" and disable it.
* **Vim** - add `:set backupcopy=yes` in your settings.

## Source Maps

When a JavaScript exception occurs, you'll often want to know what file and line is generating this error. Since webpack outputs files into one or more bundles, it can be inconvenient to trace the file.

**Source maps** intend to fix this problem. There are a lot of [different options](/configuration/devtool) - each with their own advantages and disadvantages. To get started, we'll use this one:

```js
devtool: "cheap-eval-source-map"
```

## Choosing a Tool

webpack can be used with **watch mode**. In this mode webpack will watch your files, and recompile when they change.
**webpack-dev-server** provides an easy to use development server with fast live reloading. If you already have a development server and want full flexibility, **webpack-dev-middleware** can be used as middleware.

webpack-dev-server and webpack-dev-middleware use in-memory compilation, meaning that the bundle will not be saved to disk. This makes compiling faster and results in less mess on your file system.

In most cases **you'll want to use webpack-dev-server**, since it's the easiest to get started with and offers much functionality out-of-the-box.

### webpack Watch Mode

webpack's watch mode watches files for changes. If any change is detected, it'll run the compilation again.

We also want a nice progress bar while it's compiling. Let's run the command:

```bash
webpack --progress --watch
```

Make a change in one of your files and hit save. You should see that it's recompiling.

Watch mode makes no assumptions about a server, so you will need to provide your own. An easy server is [`serve`](https://github.com/tj/serve). After installing (`npm i serve -g`), you can execute it in the directory where the outputted files are:

```bash
serve
```

After each compilation, you will need to manually refresh your browser to see the changes.

### webpack-dev-server

webpack-dev-server provides you with a server and live reloading. This is easy to setup.

To prepare, make sure you have a `index.html` file that points to your bundle. Assuming that `output.filename` is `bundle.js`:

```html
<script src="/bundle.js"></script>
```

Start with installing `webpack-dev-server` from npm:

```bash
npm install webpack-dev-server --save-dev
```

When it's done installing, you should be able to use `webpack-dev-server` like this:

```bash
webpack-dev-server --open
```

T> If your console says it can't find the command, try running `node_modules/.bin/webpack-dev-server`. Optimally you would add the command to your `package.json`, like this: `"scripts": { "start": "webpack-dev-server" }`.

The command above should automatically open your browser on `http://localhost:8080`.

Make a change in one of your files and hit save. You should see that the console is recompiling. After that's done, the page should be refreshed. If nothing happens in the console, you may need to fiddle with [`watchOptions`](/configuration/dev-server#devserver-watchoptions-).

Now you have live reloading working, you can take it even a step further: Hot Module Replacement. This is an interface that makes it possible to swap modules **without a page refresh**. Find out how to [configure HMR](/how-to/hot-module-reload).

By default **inline mode** is used. This mode injects the client - needed for live reloading and showing build errors - in your bundle. With inline mode you will get build errors and warnings in your DevTools console.

webpack-dev-server can do many more things such as proxying requests to your backend server. For more configuration options, see the [**devServer documentation**](/configuration/dev-server).

### webpack-dev-middleware

webpack-dev-middleware works for connect-based middleware stacks. This can be useful if you already have a Node.js server or if you want to have full control over the server.

The middleware will cause webpack to compile files in-memory. When a compilation is running, it will delay the request to a file until the compilation is done.

W> This is intended for advanced users. webpack-dev-server is much easier to use.

Start with installing the dependencies from npm:

```bash
npm install express webpack-dev-middleware --save-dev
```

After installing, you can use the middleware like this:

```js
var express = require("express");
var webpackDevMiddleware = require("webpack-dev-middleware");
var webpack = require("webpack");
var webpackConfig = require("./webpack.config");

var app = express();
var compiler = webpack(webpackConfig);

app.use(webpackDevMiddleware(compiler, {
  publicPath: "/" // Same as `output.publicPath` in most cases.
}));

app.listen(3000, function () {
  console.log("Listening on port 3000!");
});
```

Depending on what you've used in `output.publicPath` and `output.filename`, your bundle should now be available on `http://localhost:3000/bundle.js`.

By default, **watch mode** is used. It's also possible to use **lazy mode**, which will only recompile on a request to the entry point.

To compile only on a request to the entry `bundle.js`:

```js
app.use(webpackDevMiddleware(compiler, {
  lazy: true,
  filename: "bundle.js" // Same as `output.filename` in most cases.
}));
```

There are many more options you can use. For all configuration options, see the [**devServer documentation**](/configuration/dev-server).


## References

* [SurviveJS - Automatic Browser Refresh](http://survivejs.com/webpack/developing-with-webpack/automatic-browser-refresh/)

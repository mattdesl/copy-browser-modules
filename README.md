Collects browser modules from `node_modules` and dumps them somewhere as commonjs
Packages/A style packages (note that this is similar to but not the same as
node modules), ready to use with requirejs.

First, it scans the tree of `node_modules`, detecting and erroring if browser
modules overlap. There is no support for node style nesting dependencies and
search path from each module.

Then it takes that list of modules and copies them to the target directory.

An example:


```
var copyBrowserTo = require('copy-browser-modules');

copyBrowserTo(__dirname, 'public/js/components')
    .then(function() {
        console.log("Modules copied to 'public/js/components'")
    }).catch(function (err) {
        console.warn("An error occurred");
        console.warn(err.stack);
    });
```
.

Module format
-------------

Modules are expected to end up in something approaching CommonJS Packages/A
style. That means no nested dependencies, no `node_modules`, but `package.json`
is still used. Because we're coopting the npm registry here, sometimes we need
different information between the node and browser versions of things. If you put
an `browser` property in your `package.json` with a truthy value, the module
will be considered browser-friendly. If that property is set to an object, the
written `package.json` in the built tree will have these properties merged into
the root and `browser` left out.

This tool respects the `files` key, so a browser-only extract can be provided
by providing an `browser.files` property.

Overrides
---------

The `package.json` for your app can include a section like so:

```
    "browser": {
        "overrides": {
            "packagename": {
```

which will be used as the `browser` property for each package's `package.json`
instead of what's there, allowing applications to use libraries that don't
follow the convention yet despite shipping browser modules.

This is only supported at the root as this is not to be a convention for the
consumption of others, but only used in the interim until the convention
catches on for browser packages in the registry. Please make PRs against upstream
`package.json` files!

# `.fusionrc.js`

Fusion supports a `.fusionrc.js` in the root directory of your application. This must be a CommonJS module exporting an object.

This configuration object supports the following fields:

## `babel`

### Adding plugins/presets and excluding files from babel compilagtion

For example, to add your own Babel plugins/preset:

```js
module.exports = {
  babel: {
    presets: ["some-babel-preset"],
    plugins: ["some-babel-plugin"],
    exclude: moduleName => /\/node_modules\//.test(moduleName)
  }
};
```

## `assumeNoImportSideEffects`

By default this is `false`.

Setting this to `true` enables the assumption that modules in your code do not have any import side effects. This assumption allows for more powerful tree shaking and pruning of unused imports.

This option may be useful if you import modules that use Node.js built-ins. This option makes it easier to avoid unintentionally including server-specific code in the browser bundle.

For specifics regarding implementation details, see:
 - https://webpack.js.org/guides/tree-shaking/#mark-the-file-as-side-effect-free
 - https://github.com/webpack/webpack/tree/master/examples/side-effects
 - https://stackoverflow.com/questions/49160752/what-does-webpack-4-expect-from-a-package-with-sideeffects-false/49203452#49203452

In the future, it is possible that some form of this behavior may be turned on by default (with ways to opt-out instead).

## `nodeBuiltins`

This is an optional property that can be used to override the Fusion.js defaults for https://webpack.js.org/configuration/node/ in the browser bundle.

```js
module.exports = {
  nodeBuiltins: {
    Buffer: true
  }
};
```

## `overrideWebpackConfig`

Allows to customize [webpack configuration](https://webpack.js.org/concepts). 

Pass a function that takes a [`webpackConfig`](https://webpack.js.org/configuration/) object created by fusion and return a config with any modifications supported by webpack.

Example:

```js
const SimpleProgressPlugin = require('simple-progress-webpack-plugin');

module.exports = {
  overrideWebpackConfig: (webpackConfig) => {
    // Debug things:
    console.log(webpackConfig);

    // Pass your own plugins:
    webpackConfig.plugins = webpackConfig.plugins.map(plugin => {
      // console.log(plugin.constructor.name);

      if (plugin.constructor.name === 'ProgressPlugin') {
        return new SimpleProgressPlugin({
          format: 'expanded'
        })
      }

      return plugin;
    });
    
    // Don't forget to return it
    return webpackConfig;
  }
};
```




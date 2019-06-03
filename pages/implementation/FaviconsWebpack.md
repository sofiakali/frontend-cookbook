# Favicons in javascript applications

We use [Favicons webpack plugin](https://github.com/jantimon/favicons-webpack-plugin) for adding favicons to javascript applications.

Adding automatically generated favicons of different sizes and for different devices is easy with this plugin. It only requires two steps.

1. Install the package: `npm i -D favicons-webpack-plugin`
2. Add the plugin to your `webpack.config.js` file and configure it (at least add path to your favicon image):

```js
let FaviconsWebpackPlugin = require('favicons-webpack-plugin');

...

plugins: [
    new FaviconsWebpackPlugin('./src/app/assets/images/logo.png')
]
```

When you build the app **the plugin will bundle favicons with other assets and javascript**. Read about advanced configuration [in the documentation](https://github.com/jantimon/favicons-webpack-plugin).



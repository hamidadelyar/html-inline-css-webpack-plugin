# html-inline-css-webpack-plugin

[![MIT Licence](https://badges.frapsoft.com/os/mit/mit.svg?v=103)](https://opensource.org/licenses/mit-license.php)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/Runjuu/html-inline-css-webpack-plugin/pulls)
[![Total downloads](https://img.shields.io/npm/dm/html-inline-css-webpack-plugin.svg)](https://www.npmjs.com/package/html-inline-css-webpack-plugin)
[![npm version](https://badge.fury.io/js/html-inline-css-webpack-plugin.svg)](https://www.npmjs.com/package/html-inline-css-webpack-plugin)

Convert external stylesheet to embedded stylesheet, aka document stylesheet.

```
<link rel="stylesheet" /> => <style>...<style/>
```

Require [mini-css-extract-plugin](https://github.com/webpack-contrib/mini-css-extract-plugin) and [html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin)

## Install

#### NPM

```bash
npm i html-inline-css-webpack-plugin -D
```

#### Yarn

```bash
yarn add html-inline-css-webpack-plugin -D
```

## Minimal example

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const HTMLInlineCSSWebpackPlugin = require('html-inline-css-webpack-plugin')
  .default

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[name].css',
      chunkFilename: '[id].css',
    }),
    new HtmlWebpackPlugin(),
    new HTMLInlineCSSWebpackPlugin(),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
    ],
  },
}
```

## Config

```typescript
interface Config {
  filter?(fileName: string): boolean
  leaveCSSFile?: boolean
  replace?: {
    target: string
    position?: 'before' | 'after'
    removeTarget?: boolean
  }
  attributes?: { [name: string]: string }
}
```

### filter(optional)

```typescript
filter?(fileName: string): boolean
```

Return `true` to make current file internal, otherwise ignore current file. Include both css file and html file name.

##### example

```typescript
...
  new HTMLInlineCSSWebpackPlugin({
    filter(fileName) {
      return fileName.includes('main');
    },
  }),
...
```

### leaveCSSFile(optional)

if `true`, it will leave CSS files where they are when inlining

### replace(optional)

```typescript
replace?: {
  target: string
  position?: 'before' | 'after' // default is 'before'
  removeTarget?: boolean // default is false
}
```

A config for customizing the location of injection, default will add internal style sheet before the `</head>`

### attributes(optional)

Allows the adding of attributes to the style tag

##### example 1

```typescript
...
  new HTMLInlineCSSWebpackPlugin({
    replace: {
      attributes: {
        'type': "text/css"
      },
    },
  }),
...
```

```html
<style type="text/css">
  /* inlined css */
</style>
```

##### example 2

```typescript
...
  new HTMLInlineCSSWebpackPlugin({
    replace: {
      attributes: {
        'amp-custom': ""
      },
    },
  }),
...
```

```html
<style amp-custom="">
  /* inlined css */
</style>
```

Useful for amp pages

#### target

A target for adding the internal style sheet

#### position(optional)

Add internal style sheet `before`/`after` the `target`

#### removeTarget(optional)

if `true`, it will remove the `target` from the output HTML

> Please note that HTML comment is removed by default by the `html-webpack-plugin` in production mode. [#16](https://github.com/Runjuu/html-inline-css-webpack-plugin/issues/16#issuecomment-527884514)

##### example

```html
<head>
  <!-- inline_css_plugin -->
  <style>
    /* some hard code style */
  </style>
</head>
```

```typescript
...
  new HTMLInlineCSSWebpackPlugin({
    replace: {
      removeTarget: true,
      target: '<!-- inline_css_plugin -->',
    },
  }),
...
```

###### output:

```html
<head>
  <style>
    /* style from *.css files */
  </style>
  <style>
    /* some hard code style */
  </style>
</head>
```

This repo is a collection of simple demos of Webpack.

These demos are purposely written in a simple and clear style. You will find no difficulty in following them to learn the powerful tool.

## How to use

First, install [Webpack](https://www.npmjs.com/package/webpack) and [webpack-dev-server](https://www.npmjs.com/package/webpack-dev-server) globally.

```bash
$ npm i -g webpack webpack-dev-server
```

Then, clone the repo.

```bash
$ git clone https://github.com/ruanyf/webpack-demos.git
```

Install the dependencies.

```bash
$ cd webpack-demos
$ npm install
```

Now, play with the source files under the repo's demo* directories.

```bash
$ cd demo01
$ npm run dev
```

If the above command doesn't open your browser automaticly, you have to visit http://127.0.0.1:8080 by yourself.

## 前言：Webpack是什么

Webpack is a front-end tool to build JavaScript module scripts for browsers.

It can be used similar to Browserify, and do much more.

```bash
$ browserify main.js > bundle.js
# be equivalent to
$ webpack main.js bundle.js
```

Webpack需要一个配置文件`webpack.config.js`， which is just a CommonJS module.

```javascript
// webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
```

有了`webpack.config.js`后，你可以不带任何参数地调用Webpack。

```bash
$ webpack
```

你需要知道一些命令行选项。

- `webpack` – building for development
- `webpack -p` – building for production (minification)
- `webpack --watch` – for continuous incremental building
- `webpack -d` – including source maps
- `webpack --colors` – making building output pretty

你可以用如下方法在你的`package.json`里头定制`scripts`字段。

```javascript
// package.json
{
  // ...
  "scripts": {
    "dev": "webpack-dev-server --devtool eval --progress --colors",
    "deploy": "NODE_ENV=production webpack -p"
  },
  // ...
}
```

## 索引

1. [Entry file](#demo01-entry-file-source)
1. [Multiple entry files](#demo02-multiple-entry-files-source)
1. [Babel-loader](#demo03-babel-loader-source)
1. [CSS-loader](#demo04-css-loader-source)
1. [Image loader](#demo05-image-loader-source)
1. [CSS Module](#demo06-css-module-source)
1. [UglifyJs Plugin](#demo07-uglifyjs-plugin-source)
1. [HTML Webpack Plugin and Open Browser Webpack Plugin](#demo08-html-webpack-plugin-and-open-browser-webpack-plugin-source)
1. [Environment flags](#demo09-environment-flags-source)
1. [Code splitting](#demo10-code-splitting-source)
1. [Code splitting with bundle-loader](#demo11-code-splitting-with-bundle-loader-source)
1. [Common chunk](#demo12-common-chunk-source)
1. [Vendor chunk](#demo13-vendor-chunk-source)
1. [Exposing Global Variables](#demo14-exposing-global-variables-source)
1. [React router](#demo15-react-router-source)

## Demo01: Entry file ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo01))

Entry file is a file which Webpack reads to build `bundle.js`.

举个栗子，`main.js`是一个入口文件。

```javascript
// main.js
document.write('<h1>Hello World</h1>');
```

index.html

```html
<html>
  <body>
    <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```

Webpack follows `webpack.config.js` to build `bundle.js`.

```javascript
// webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
```

启动服务器，访问 http://127.0.0.1:8080 。

```bash
$ cd demo01
$ npm run dev
```

## Demo02: Multiple entry files ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo02))

多个入口文件也是被允许的。这对于每个页面有不同entry的多页面app是有用的。

```javascript
// main1.js
document.write('<h1>Hello World</h1>');

// main2.js
document.write('<h2>Hello Webpack</h2>');
```

index.html

```html
<html>
  <body>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: {
    bundle1: './main1.js',
    bundle2: './main2.js'
  },
  output: {
    filename: '[name].js'
  }
};
```

## Demo03: Babel-loader ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo03))

loader在Webpack的构建开始之前，对app的资源文件进行预处理。[preprocessor]([more info](https://webpack.js.org/concepts/loaders/))

举个栗子，[Babel-loader](https://www.npmjs.com/package/babel-loader)可以把JSX/ES6文件转换成普通的JS文件，然后Webpack才开始对这些文件进行构建（Webpack本身只能处理js文件）。 [Webpack的官方文档有loader的完整清单。](https://webpack.js.org/loaders/).

`main.jsx`是JSX文件。

```javascript
// main.jsx
const React = require('react');
const ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.querySelector('#wrapper')
);
```

index.html

```html
<html>
  <body>
    <div id="wrapper"></div>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,//对除了这个路径下的，所有符合/\.jsx?$/的文件进行处理
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      }
    ]
  }
};
```

以上使用`babel-loader`的片段需要Babel的预设[babel-preset-es2015](https://www.npmjs.com/package/babel-preset-es2015)和[babel-preset-react](https://www.npmjs.com/package/babel-preset-react)来转译[transpile]ES6和React。

## Demo04: CSS-loader ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo04))

Webpack允许你在js文件里头引用CSS文件, 然后用[CSS-loader](https://github.com/webpack-contrib/css-loader)对css文件做预处理。

main.js

```javascript
require('./app.css');
```

app.css

```css
body {
  background-color: blue;
}
```

index.html

```html
<html>
  <head>
    <script type="text/javascript" src="bundle.js"></script>
  </head>
  <body>
    <h1>Hello World</h1>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules:[
      {
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ]
      },
    ]
  }
};
```

注意，你必须使用两个loader来处理CSS文件。第一个是[CSS-loader](https://www.npmjs.com/package/css-loader)，用来读取css文件。第二个是 [Style-loader](https://www.npmjs.com/package/style-loader)用来把`<style>`标签插入到HTML里。

然后，启动服务器。

```bash
$ cd demo04
$ npm run dev
```

事实上, Webpack插入了一个内部样式表[internal style sheet]到`index.html`。

```html
<head>
  <script type="text/javascript" src="bundle.js"></script>
  <style type="text/css">
    body {
      background-color: blue;
    }
  </style>
</head>
```

## Demo05: Image loader ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo05))

Webpack还可以在js文件里头引用图片。

main.js

```javascript
var img1 = document.createElement("img");
img1.src = require("./small.png");
document.body.appendChild(img1);

var img2 = document.createElement("img");
img2.src = require("./big.png");
document.body.appendChild(img2);
```

index.html

```html
<html>
  <body>
    <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules:[
      {
        test: /\.(png|jpg)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 8192
            }
          }
        ]
      }
    ]
  }
};
```

[url-loader](https://www.npmjs.com/package/url-loader)把图片文件转换成`<img>`标签。如果图片小于8192字节，它将被转换成数据URL[Data URL]；否则，它将被转换成普通URL。

启动服务器后，`small.png`和`big.png`有如下URL。

```html
<img src="data:image/png;base64,iVBOR...uQmCC">
<img src="4853ca667a2b8b8844eb2693ac1b2578.png">
```

## Demo06: CSS Module ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo06))

`css-loader?modules` (查询参数模块)使得[CSS Module](https://github.com/css-modules/css-modules)能够给JS模块加上局部作用域的[local scoped]CSS。你可以在css文件里头用`:global(selector)`来将它关闭。 ([more info](https://css-modules.github.io/webpack-demo/)).

index.html

```html
<html>
<body>
  <h1 class="h1">Hello World</h1>
  <h2 class="h2">Hello Webpack</h2>
  <div id="example"></div>
  <script src="./bundle.js"></script>
</body>
</html>
```

app.css

```css
/* local scope */
.h1 {
  color:red;
}

/* global scope */
:global(.h2) {
  color: blue;
}
```

main.jsx

```javascript
var React = require('react');
var ReactDOM = require('react-dom');
var style = require('./app.css');

ReactDOM.render(
  <div>
    <h1 className={style.h1}>Hello World</h1>
    <h2 className="h2">Hello Webpack</h2>
  </div>,
  document.getElementById('example')
);
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      },
      {
        test: /\.css$/,
        use: [
          {
            loader: 'style-loader'
          },
          {
             loader: 'css-loader',
             options: {
               modules: true
             }
          }
        ]
      }
    ]
  }
};
```

启动服务器。

```bash
$ cd demo06
$ npm run dev
```

访问http://127.0.0.1:8080，你会发现只有第二个`h1`是红的，因为它的css是局部作用域的[local scoped]；两个`h2`都是蓝的，因为它的css是全局作用域的[global scoped]。

## Demo07: UglifyJs Plugin ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo07))

Webpack有一个plugin系统来扩展它的函数。举个栗子，[UglifyJs Plugin](https://webpack.js.org/plugins/uglifyjs-webpack-plugin/)将会压缩输出的(`bundle.js`)的JS代码体积。

main.js

```javascript
var longVariableName = 'Hello';
longVariableName += ' World';
document.write('<h1>' + longVariableName + '</h1>');
```

index.html

```html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack');
var UglifyJsPlugin = require('uglifyjs-webpack-plugin');

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new UglifyJsPlugin()
  ]
};
```

启动服务器后，`main.js`将会被压缩[minified]成下面这个样子。

```javascript
var o="Hello";o+=" World",document.write("<h1>"+o+"</h1>")
```

## Demo08: HTML Webpack Plugin and Open Browser Webpack Plugin ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo08))

这个demo展示了如何加载第三方plugins.

[html-webpack-plugin](https://github.com/ampedandwired/html-webpack-plugin)将会创建`index.html`，然后[open-browser-webpack-plugin](https://github.com/baldore/open-browser-webpack-plugin)将会在Webpack加载时打开一个新的浏览器Tab。

main.js

```javascript
document.write('<h1>Hello World</h1>');
```

webpack.config.js

```javascript
var HtmlwebpackPlugin = require('html-webpack-plugin');
var OpenBrowserPlugin = require('open-browser-webpack-plugin');

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new HtmlwebpackPlugin({
      title: 'Webpack-demos',
      filename: 'index.html'
    }),
    new OpenBrowserPlugin({
      url: 'http://localhost:8080'
    })
  ]
};
```

启动服务器。

```bash
$ cd demo08
$ npm run dev
```

现在你不需要自己手动打开`index.html`了。Webpack帮你完成了。

## Demo09: Environment flags ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo09))

你可以enable一些代码only in开发环境with环境标记[flag].

main.js

```javascript
document.write('<h1>Hello World</h1>');

if (__DEV__) {
  document.write(new Date());
}
```

index.html

```html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack');

var devFlagPlugin = new webpack.DefinePlugin({
  __DEV__: JSON.stringify(JSON.parse(process.env.DEBUG || 'false'))
});

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [devFlagPlugin]
};
```

现在，pass环境变量到webpack。打开`demo09/package.json`，你会找到像下边这样的`scripts`字段。

```javascript
// package.json
{
  // ...
  "scripts": {
    "dev": "cross-env DEBUG=true webpack-dev-server --open",
  },
  // ...
}
```

启动服务器。

```javascript
$ cd demo09
$ npm run dev
```

## Demo10: Code splitting ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo10))

对于一个大的web应用来说，把所有代码放进单个文件的效率很低。你可以用Webpack把一个大的JS文件分割成多个块[chunk]。特别是有些代码只有在特定条件下才需要用到, 这些块将被按需加载。

Webpack使用`require.ensure`来定义一个分割点[split point] ([官方文档](http://webpack.github.io/docs/code-splitting.html)).

```javascript
// main.js
require.ensure(['./a'], function (require) {
  var content = require('./a');
  document.open();
  document.write('<h1>' + content + '</h1>');
  document.close();
});
```

`require.ensure`告诉Webpack，`./a.js`应该从`bundle.js`里头分割出来，并构建成单个js文件。

```javascript
// a.js
module.exports = 'Hello World';
```

现在Webpack注意依赖关系，输出的文件和运行时的东西。`index.html`和`webpack.config.js`这两个文件里头可以没有任何冗余。

```html
<html>
  <body>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
```

启动服务器。

```bash
$ cd demo10
$ npm run dev
```

表面上，你不会感受到任何不同。然而，实际上Webpack把`main.js`和`a.js`构建成两个不同的块[chunk] (`bundle.js`和`0.bundle.js`)，并且在需要时从`bundle.js`载入`0.bundle.js`。

## Demo11: Code splitting with bundle-loader ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo11))

另外一个分割代码的方法是使用[bundle-loader](https://www.npmjs.com/package/bundle-loader)。

```javascript
// main.js

// Now a.js is requested, it will be bundled into another file
var load = require('bundle-loader!./a.js');

// To wait until a.js is available (and get the exports)
//  you need to async wait for it.
load(function(file) {
  document.open();
  document.write('<h1>' + file + '</h1>');
  document.close();
});
```

`require('bundle-loader!./a.js')`告诉Webpack从另一个块载入`a.js`。

现在Webpack将把`main.js`构建成`bundle.js`，把`a.js`构建成`0.bundle.js`。

## Demo12: Common chunk ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo12))

当多个scripts有公共的块时，你可以用[CommonsChunkPlugin](https://webpack.js.org/plugins/commons-chunk-plugin/)提取出相同的部分到一个单独的文件， 它对于浏览器缓存和节省带宽来说是很有用的。

```javascript
// main1.jsx
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>Hello World</h1>,
  document.getElementById('a')
);

// main2.jsx
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h2>Hello Webpack</h2>,
  document.getElementById('b')
);
```

index.html

```html
<html>
  <body>
    <div id="a"></div>
    <div id="b"></div>
    <script src="commons.js"></script>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
  </body>
</html>
```

上述的`commons.js`是`main1.jsx`和`main2.jsx`的公有代码块。正如你设想的，`commons.js`包含了`react`和`react-dom`。

webpack.config.js

```javascript
var webpack = require('webpack');

module.exports = {
  entry: {
    bundle1: './main1.jsx',
    bundle2: './main2.jsx'
  },
  output: {
    filename: '[name].js'
  },
  module: {
    rules:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      },
    ]
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: "commons",
      // (the commons chunk name)

      filename: "commons.js",
      // (the filename of the commons chunk)
    })
  ]
}
```

## Demo13: Vendor chunk ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo13))

你还可以用CommonsChunkPlugin，将第三方库[vendor]从一个script提取到一个单独的文件。

main.js

```javascript
var $ = require('jquery');
$('h1').text('Hello World');
```

index.html

```html
<html>
  <body>
    <h1></h1>
    <script src="vendor.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack');

module.exports = {
  entry: {
    app: './main.js',
    vendor: ['jquery'],
  },
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      filename: 'vendor.js'
    })
  ]
};
```

以上代码中，`entry.vendor: ['jquery']`告诉Webpack`jquery`应该被包括进公有块`vendor.js`里。

如果你想要一个模块[module]作为一个全局变量，在每个模块都能访问到，比如不写`require("jquery")`就让`$`和`jQuery`在每个模块都能访问到，你应该使用`ProvidePlugin` ([官方文档](https://webpack.js.org/plugins/provide-plugin/))，`ProvidePlugin`可以自动加载模块，而不是必须在每个地方import或require那些模块。

```javascript
// main.js
$('h1').text('Hello World');


// webpack.config.js
var webpack = require('webpack');

module.exports = {
  entry: {
    app: './main.js'
  },
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.ProvidePlugin({
      $: 'jquery',
      jQuery: 'jquery'
    })
  ]
};
```

当然了，在这个情况下，你应该自己全局载入[load]`jquery.js`。

## Demo14: Exposing global variables ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo14))

如果你想要使用全局变量，且不想在Webpack包里头include它们，你可以在`webpack.config.js` ([官方文档](https://webpack.js.org/configuration/externals/))里头enable `externals`字段。

举个例子，我们有`data.js`文件。

```javascript
// data.js
var data = 'Hello World';
```

index.html

```html
<html>
  <body>
    <script src="data.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
```

注意，Webpack将只会构建`bundle.js`，不会构建`data.js`。

我们可以expose `data`作为一个全局变量。

```javascript
// webpack.config.js
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      },
    ]
  },
  externals: {
    // require('data') is external and available
    //  on the global var data
    'data': 'data'
  }
};
```

现在，你在你的script中require`data`作为一个模块变量，但实际上它是一个全局变量。

```javascript
// main.jsx
var data = require('data');
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>{data}</h1>,
  document.body
);
```

你也可以把`react`和`react-dom`放进`externals`，这将大幅缩短构建时间和构建完的`bundle.js`的大小。

## Demo15: React router ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo15))

这个demo是用webpack来构建[React-router](https://github.com/rackt/react-router/blob/0.13.x/docs/guides/overview.md)的官方例子。

让我们来想象一个有dashboard, inbox, and calendar的小应用。

```
+---------------------------------------------------------+
| +---------+ +-------+ +--------+                        |
| |Dashboard| | Inbox | |Calendar|      Logged in as Jane |
| +---------+ +-------+ +--------+                        |
+---------------------------------------------------------+
|                                                         |
|                        Dashboard                        |
|                                                         |
|                                                         |
|   +---------------------+    +----------------------+   |
|   |                     |    |                      |   |
|   | +              +    |    +--------->            |   |
|   | |              |    |    |                      |   |
|   | |   +          |    |    +------------->        |   |
|   | |   |    +     |    |    |                      |   |
|   | |   |    |     |    |    |                      |   |
|   +-+---+----+-----+----+    +----------------------+   |
|                                                         |
+---------------------------------------------------------+
```

webpack.config.js

```javascript
module.exports = {
  entry: './index.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ]
      },
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      },
    ]
  }
};
```

index.js

```javascript
import React from 'react';
import { render } from 'react-dom';
import { BrowserRouter, Switch, Route, Link } from 'react-router-dom';

import './app.css';

class App extends React.Component {
  render() {
    return (
      <div>
        <header>
          <ul>
            <li><Link to="/app">Dashboard</Link></li>
            <li><Link to="/inbox">Inbox</Link></li>
            <li><Link to="/calendar">Calendar</Link></li>
          </ul>
          Logged in as Jane
        </header>
        <main>
          <Switch>
            <Route exact path="/" component={Dashboard}/>
            <Route path="/app" component={Dashboard}/>
            <Route path="/inbox" component={Inbox}/>
            <Route path="/calendar" component={Calendar}/>
            <Route path="*" component={Dashboard}/>
          </Switch>
        </main>
      </div>
    );
  }
};

class Dashboard extends React.Component {
  render() {
    return (
      <div>
        <p>Dashboard</p>
      </div>
    );
  }
};

class Inbox extends React.Component {
  render() {
    return (
      <div>
        <p>Inbox</p>
      </div>
    );
  }
};

class Calendar extends React.Component {
  render() {
    return (
      <div>
        <p>Calendar</p>
      </div>
    );
  }
};

render((
  <BrowserRouter>
    <Route path="/" component={App} />
  </BrowserRouter>
), document.querySelector('#app'));
```

index.html

```html
<html>
  <body>
    <div id="app"></div>
    <script src="/bundle.js"></script>
  </body>
</htmL>
```

启动服务器。

```bash
$ cd demo15
$ npm run dev
```

## Useful links

- [Webpack docs](https://webpack.js.org/concepts/)
- [webpack-howto](https://github.com/petehunt/webpack-howto), by Pete Hunt
- [SurviveJS Webpack book](https://survivejs.com/webpack/introduction/), by Juho Vepsäläinen
- [Diving into Webpack](https://web-design-weekly.com/2014/09/24/diving-webpack/), by Web Design Weekly
- [Webpack and React is awesome](http://www.christianalfoni.com/articles/2014_12_13_Webpack-and-react-is-awesome), by Christian Alfoni
- [Browserify vs Webpack](https://medium.com/@housecor/browserify-vs-webpack-b3d7ca08a0a9), by Cory House

## License

MIT

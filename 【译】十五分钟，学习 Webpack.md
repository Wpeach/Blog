> * 原文地址：[Learn Webpack in 15 Minutes](https://tutorialzine.com/2017/04/learn-webpack-in-15-minutes)
> * 原文作者：Danny Markov
> * 译者：[Wpeach](https://github.com/Wpeach/Blog)

由于 JavaScript 应用程序的复杂性不断增加，构建工具已成为 web 开发中不可或缺的一部分。Bundlers 允许我们去打包、编译和管理现代 web 项目需要的众多资源文件和依赖库。


在本教程中，我们将了解 [webpack](https://webpack.github.io/)，一个强大的开源打包和预处理器，可以处理大量复杂多样的任务。我们将向您展示如何去编写 modules， bundle 代码和使用一些 loader 插件。本教程为 webpack 初学者设计的，要求有一些 JavaScript 基础。

![webpack](https://user-gold-cdn.xitu.io/2019/1/24/1687e8e50accbc6c?w=785&h=262&f=jpeg&s=7911)

### 为什么选择 Webpack？

就像 web 开发的其它方面一样，构建工具的也没有一个标准。现在开发者可以在 [Webpack](https://webpack.github.io/)、[Gulp](https://gulpjs.com/)、[Bowserify](http://browserify.org/)、[NPM scripts](https://docs.npmjs.com/misc/scripts)、[Grunt](https://gruntjs.com/) 等类似的十余种构建工具。所有这些工具在在底层实现有所不同，但总体还是非常相似的，所以大多数时候你可以根据个人喜好和开发的项目，来选择合适的构建工具。

你可以通过以下优缺点，来判断 webpack 是否合适：

**优点：**

* 可以很好的用于单页应用
* 同时支持 `require()` 和 `import` 模块语法
* 允许非常前沿的 [code splitting](https://webpack.js.org/guides/code-splitting/#components/sidebar/sidebar.jsx) 特性
* [热加载](https://webpack.js.org/concepts/hot-module-replacement/) 可以让 React、Vue.js 和其它类似框架的本地开发更快
* 它是[《2016 JavaScript 调查》](https://stateofjs.com/2016/buildtools/)中最受欢迎的构建工具

**缺点：**

* 不适合 web 开发的初学者
* 对于 CSS、图片和其它非 JS 资源文件时，需要先混淆处理
* 文档可以再优化
* 变化很大，甚至 2016 年的教程都已经算过时了

---

### 1. 安装

安装 webpack 最简单的方式是通过包管理器。我们将使用 [npm](https://www.npmjs.com/) 你也随意使用 [Yarn](https://yarnpkg.com/zh-Hans/) 或其它熟悉的替代。不管是 npm 还是 Yarn，你都需要现有一个带 [Node.js](https://nodejs.org/en/) 的运行环境和 *package.json* 文件再继续。

推荐局部安装（不带 `-g` 标签），确保每个人运行你的项目时都是一样的 webpack 版本。

```
npm install webpack --save-dev
```

一旦我们安装完，最好通过 Node.js 脚本来运行 webpack，可以在添加以下代码到你的 package.json：

``` js
//...
    "scripts": {
        "build": "webpack -p",
        "watch": "webpack --watch"
    },
//...
```

现在我们可以通过在命令行输入 `npm run build` 来让 webpack 打包我们的文件（-p 选项可以压缩生产环境下打包后的代码）。运行 `npm run watch` 将启动一个进程，当检测到文件更改时，会自动打包我们的文件。

设置的最后一部分是告诉 webpack 要打包哪些文件，推荐新创建一个配置文件。

### 2. Webpack 配置文件

在这我们看一下配置文件最基础的写法，不要被误导 - webpack 配置文件非常强大，在不同的项目中变化很大，在某些情况下会变得非常复杂。

在你项目的根目录下新增一个名为 *webpack.config.js* 的文件。

**webpack.config.js**

```
var path = require('path');

module.exports = {
  entry: './assets/js/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

entry 选项告诉webpack哪个是我们 Javascript 主文件。配置 entry 有许多不同的策略，但在大多数情况一个入口就足够了。

在 output 中我们指定打包的名称和路径。在 webpack 运行完后我们将把所有 JavaScript 放在一个名为 **bundle.js** 的文件中，这个唯一的 script 文件可以链接在我们的 HTML 文件：

```
<script src="./dist/bundle.js"></script>
```

这个设置足以让我们开始，稍后我们会向其添加更多的内容，但首先让我们先来看看模块是如何工作的。

---

### 3. Webpack 模块

Webpack 提供了多种使用模块的方法，大多数时候你可以选择一个你喜欢的。 在这个教程中我们会使用到 ES6 的 `import` 语法。

我们想要添加一个模块来迎接我们的用户，我们创建一个名为 *greeter.js* 文件，将它导出为一个简单的函数

**greeter.js**

```
function greet() {
    console.log('Have a great day!');
};

export default greet;
```

为了使用这个模块，我们必须引入它并在 entry 中调用它，你可以在 index.js 中看到配置文件。

**index.js**

```
import greet from './greeter.js';

console.log("I'm the entry point");
greet();
```

当我们 `npm run build` 运行打包时，在浏览器中打开我们的 HTML 可以看到：

![console-greet](https://user-gold-cdn.xitu.io/2019/1/24/1687e8e50bdf9922?w=554&h=211&f=png&s=14574)

在我们的 entry 和 greeter 模块已经编译成一个名为 *bundle.js* 的文件，且已经在浏览器生效。这是迄今为止发生事情的简单流程图。

![webpack-flow](https://user-gold-cdn.xitu.io/2019/1/24/1687e8e50c196268?w=785&h=272&f=png&s=13985)


### 4. 需要的库

我们想要让我们的应用程序在一周中的某一天问候用户，我们可以直接在我们的 greeter 模块引入 [moment.js](http://momentjs.com/)。

首先，我们需要通过 npm 来安装这个库

```
npm install moment --save
```

在我们再次打包更新之后，在浏览器的控制台我们将收到一下信息：


![weekday-greet](https://user-gold-cdn.xitu.io/2019/1/24/1687e8e511b725cd?w=554&h=211&f=png&s=15325)

这时我们的流程图是这样的：

![/webpack-flow-2](https://user-gold-cdn.xitu.io/2019/1/24/1687e8e50aa407ff?w=785&h=370&f=png&s=17728)

> 注意，在引入库时有更高级的用法，不在本文章的范围内，你可以[点这](https://webpack.js.org/guides/code-splitting/)了解更多。

---

### 5.Loaders

Loader 是 webpack 在打包过程中执行任务对文件进行预处理或后处理的方式。比如它可以编译 TypeScript, 加载 Vue.js 组件，渲染模板等等。大多数 loaders 是社区编写的，受欢迎的 loaders 清单可以看[这里](https://webpack.js.org/loaders/)

假设我们想要添加一个 linter 到我们的项目中去检查我们 JavaScript 代码的错误。 我们可以引入 [JSHint loader](https://github.com/webpack-contrib/jshint-loader) 来实现，它可以捕获各种不良实践和语法错误。

首先我们需要安装 JSHint 和 webpack 的 JSHint loader:

```
npm install jshint jshint-loader --save-dev
```

之后我们需要在 webpack 配置文件中添加一些代码，这将初始化 loader， 告诉它要检查哪些类型的文件，以及忽略哪些文件。

**webpack.config.js**

```
var path = require('path');

module.exports = {
  entry: './assets/js/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  // Add the JSHint loader
  module: {
    rules: [{
      test: /\.js$/, // Run the loader on all .js files
      exclude: /node_modules/, // ignore all files in the node_modules folder
      use: 'jshint-loader'
    }]
  }
};
```

现在当 webpack 启动后，它将在命令行显示一个警告列表(那些我们所忽略的文件)

![terminal-warnings](https://user-gold-cdn.xitu.io/2019/1/24/1687e8e5108af5f5?w=685&h=265&f=png&s=45605)

因为 *moment.js* 位于 *node_modules* 文件中，它不会被 JSHint loader 检测。

![webpack-flow](https://user-gold-cdn.xitu.io/2019/1/24/1687e8e5aa9d1649?w=785&h=370&f=png&s=25000)


### 延伸阅读

我们对 webpack 的介绍到此为止。 因为这是一个针对初学者的课程，所以我们尝试只覆盖最有用和应该知道的 webpack 概念。 我们希望这个教程有用，不要太混乱，就像标题所说的在十五分钟内可以消化。

接下来，我们计划在本教程中添加第二部分，说明如何使用 CSS 模块和其它更高级的功能。在此期间，如果你想要学习更多关于 webpack 的信息（还有更多），我们推荐你阅读这些很棒的资源

* [webpack.js.org](https://webpack.js.org/) - webpack 的官方网站，里面有很多的指南和文档。
* [Awesome webpack](https://github.com/webpack-contrib/awesome-webpack) - webpack 资源汇总
* [Webpack Example](https://github.com/webpack/webpack/tree/master/examples) - 各种webpack配置的列表
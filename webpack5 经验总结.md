<div align="center">
  <a href="https://github.com/webpack/webpack">
    <img width="200" height="200" src="https://webpack.js.org/assets/icon-square-big.svg">
  </a>
 
  <p>
   本文主要介绍，最新版本webpack 5在开发和生产环境下的常见配置、及常见错误。
</p>
</div>

----
<h1 align="center">Dependencies</h1>
  
|                                                                     Name                                                                      |     Status     |  Install Size   | Description                                                              |
| :-------------------------------------------------------------------------------------------------------------------------------------------: | :------------: | :-------------: | :----------------------------------------------------------------------- |
|                                    <a href="https://github.com/webpack/webpack"><img width="48" height="48" src="https://cdn.worldvectorlogo.com/logos/webpack-icon.svg"></a> <br>webpack                                   |  ![webpack-npm]  |  ![webpack-size]  | 模块打包器    
|                                    <a href="https://github.com/webpack/webpack"><img width="48" height="48" src="https://cdn.worldvectorlogo.com/logos/webpack-icon.svg"></a> <br>webpack-cli                                  |  ![webpack-cli-npm]  |  ![webpack-cli-size]  | webpack官方命令行工具   
|                                    <a href="https://github.com/webpack/webpack"><img width="48" height="48" src="https://cdn.worldvectorlogo.com/logos/webpack-icon.svg"></a> <br>webpack-dev-server                                 |  ![webpack-dev-server-npm]  |  ![webpack-dev-server-size]  | Serves a webpack app. Updates the browser on changes.
|                                    <a href="https://github.com/webpack/webpack"><img width="48" height="48" src="https://cdn.worldvectorlogo.com/logos/webpack-icon.svg"></a> <br>webpack-merge                               |  ![webpack-merge-npm]  |  ![webpack-merge-size]  | webpack配置文件merge    
|                                    <a href="https://github.com/webpack-contrib/style-loader">`<style>`</a>                                 |  ![style-npm]  |  ![style-size]  | Add exports of a module as style to DOM                                  |
|  <a href="https://github.com/webpack-contrib/css-loader"><img width="48" height="48" src="https://worldvectorlogo.com/logos/css-3.svg"></a> <br>css-loader  |   ![css-npm]   |   ![css-size]   | Loads CSS file with resolved imports and returns CSS code                |
 
[webpack-npm]: https://img.shields.io/npm/v/webpack.svg
[webpack-size]: https://packagephobia.com/badge?p=webpack
[webpack-cli-npm]: https://img.shields.io/npm/v/webpack-cli.svg
[webpack-cli-size]: https://packagephobia.com/badge?p=webpack-cli
[webpack-dev-server-npm]: https://img.shields.io/npm/v/webpack-dev-server.svg
[webpack-dev-server-size]: https://packagephobia.com/badge?p=webpack-dev-server
[webpack-merge-npm]: https://img.shields.io/npm/v/webpack-merge.svg
[webpack-merge-size]: https://packagephobia.com/badge?p=webpack-merge
[style-npm]: https://img.shields.io/npm/v/style-loader.svg
[style-size]: https://packagephobia.com/badge?p=style-loader
[css-npm]: https://img.shields.io/npm/v/css-loader.svg
[css-size]: https://packagephobia.com/badge?p=css-loader

<h1 align="center">Usage</h1>

Description                            |解决办法                            |
 ------------------------------------ | ------------------------------------ |
根据不同环境进行打包，共同项配置抽取出来 | 创建3个文件common.js（通用配置）,dev.js（开发环境）,prod.js（生产环境），引入webpack-merge包，参考 [环境配置](#production)
 开发环境下的跨域问题解决方案 |proxy can only proxy local domain names to the corresponding domain names, not other domain names to the corresponding domain names（devServer.proxy，只对localhost有效，即baseURL:""） |
path.resolve用法 |解析为绝对路径，可理解为cd命令，一层层拼接|
 禁止生成xx.js.LICENSE.txt文件 |webpack已集成TerserPlugin，```   new TerserPlugin({ extractComments: false,}) ```|
不打包js库，而通过cdn引入|  ```externals: { jquery: "jQuery",axios:"axios",dayjs: "dayjs",},```|
 打包生成的js文件头部加上package版本号 |```const packageinfo = require("./package.json");```|
依赖的node_modules不打包在使用的js文件中，而是全部打包到一个js文件中|```splitChunks: {chunks: "all",},```带来的问题是1个js文件过大，可采用externals优化或cacheGroups配置提取指定的module|
生产环境下文件压缩优化方案|webpack5以上版本自带TerserPlugin|
 
<h1 align="center">常见问题</h1>

| 场景 |Error Description                            |解决办法                           |
| --------- | ------------------------------------ | ------------------------------------ |
| <img width="40" height="40" src="https://cdn.worldvectorlogo.com/logos/internet-explorer-2.svg">       | `Automatic publicPath is not supported in this browser` | 参考官方文档、定义publicPath|
| <img width="40" height="40" src="https://cdn.worldvectorlogo.com/logos/internet-explorer-2.svg">        | SCRIPT5022: SecurityError |重启IE浏览器
| <img width="40" height="40" src="https://cdn.worldvectorlogo.com/logos/internet-explorer-2.svg">        | ES6、Promise等语法错误 |使用babel、target设置其编译环境
|<img width="30" height="30" src="https://cdn.worldvectorlogo.com/logos/chrome.svg"> <img width="30" height="30" src="https://cdn.worldvectorlogo.com/logos/internet-explorer-2.svg"> | axios请求参数中文乱码 | service.get(url,params)使用params参数，不要拼接到url后面
|| proxy代理不起效 |   ```devServer: {proxy: {'http://xxx/api/report': 'http://yyy/api/report',},,```proxy只能代理local domain，即"/api/**"
|  | proxy代理请求失败  <font color=red>```[webpack-dev-server] [HPM] Error occurred while proxying request localhost:3000```</font>|注意host配置，所代理的url是否配置为了127.0.0.1
|  | devtool开发环境下debug，中文乱码问题|开发环境使用“source-map”或“cheap-source-map”，不要使用带有eval函数，eval会将source-map内容转化为base64后内嵌到build.js中


<h1 align="center">webpack配置文件</h1>

#### 执行命令
```json
  "scripts": {
    "start": "webpack serve --open --config webpack.dev.js",
    "build": "webpack --config webpack.prod.js"
  },
```

#### webpack.prod.js
```javascript
const { merge } = require("webpack-merge");
const common = require("./webpack.common.js");
const TerserPlugin = require("terser-webpack-plugin");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const packageinfo = require("./package.json");
const webpack = require("webpack");

module.exports = merge(common, {
  mode: "production",
  target: ["web", "es5"],
  optimization: {
    minimizer: [
      new TerserPlugin({
        extractComments: false, //禁止生成注释到LICENSE.txt
      }),
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      title: "Production",
      filename: "index.html", // 输出文件【注意：这里的根路径是module.exports.output.path】，即上面的dist
      template: "./src/index.html", // 源模板文件，复制此结构的文件
      hash: false,
      chunks: ["index"],
    }),
    new webpack.BannerPlugin(`dfzq-web@version=${packageinfo.version}`),
  ],
});
```
#### webpack.dev.js
```javascript
const { merge } = require("webpack-merge");
const common = require("./webpack.common.js");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const webpack = require("webpack");

//使用环境变量，反之则使用root “/”
const ASSET_PATH = process.env.ASSET_PATH || "/";

module.exports = merge(common, {
  mode: "development",
  output: {
    publicPath: ASSET_PATH,
  },
  //在webpack5版本中使用默认或者'web'参数时，hot热部署是有效的。但是编译的runtime代码IE浏览器不识别，语法不支持
  //使用target: ['web','es5']，可以使编译的代码使用es5的特性，能正常在IE中使用。但是热更新失效
  //webpack5热更新失效问题只能等待官方新版本中会得到优化解决，暂时建议在开发模式中使用target: 'web',在生产模式中使用target: ['web','es5']
  target: ["web"], //热启动生效，IE无法识别runtime代码
  devtool: "eval-source-map",
  devServer: {
    port: 3000,
    open: true,
    hot: true,
    historyApiFallback: true,
    allowedHosts: "all",
    proxy: {
      "/api/**": {
        target: "http://report.orientsec.com.cn",
        secure: false,
        changeOrigin: true,
      },
    },
  },
  plugins: [
    new HtmlWebpackPlugin({
      title: "Development",
      filename: "index.html", // 输出文件【注意：这里的根路径是module.exports.output.path】，即上面的dist
      template: "./src/index.html", // 源模板文件，复制此结构的文件
      hash: false,
      chunks: ["index"],
    }),
    new webpack.DefinePlugin({
      "process.env.ASSET_PATH": JSON.stringify(ASSET_PATH),
    }),
  ],
});
```

#### webpack.common.js
```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { CleanWebpackPlugin } = require("clean-webpack-plugin"); // 清理dist文件夹
const webpack = require("webpack");
const CopyPlugin = require("copy-webpack-plugin"); //将单个文件或整个目录复制到构建目录

const publishPath = path.resolve(__dirname, "dfzq-office"); //生成的路径
module.exports = {
  entry: {
    index: path.resolve(__dirname, "./src/index.js"),
    author: path.resolve(__dirname, "./src/js/author.js"),
  },
  output: {
    filename: (chunkData) => {
      const name = chunkData.chunk.name;
      if (name === "excel"
      ) {
        return "js/[name].[chunkhash:5].min.js";
      } else {
        return "js/[name].min.js";
      }
    },
    path: publishPath,
  },
  optimization: {
    //将node_modules模板分离出来，不用打包在引用的各个js里，使得体积很大
    splitChunks: {
      chunks: "all",
    },
  },
  module: {
    rules: [
      { test: /\.txt$/, use: "raw-loader" },
      { test: /\.(css|sass|scss)$/, use: ["style-loader", "css-loader"] },
      {
        test: /\.(ico|png|jpe?g|gif)$/i,
        loader: "file-loader",
        options: {
          name: "[path][name].[ext]",
          context: "src", //上下文路径，如果没有此项，dist目录下将会多一个src文件夹
        },
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "babel-loader",
        options: {
          presets: [
            [
              "@babel/preset-env",
              {
                useBuiltIns: "usage",
                corejs: {
                  // core-js的版本
                  version: 3,
                },
                // 需要兼容的浏览器
                targets: {
                  chrome: "60",
                  firefox: "60",
                  ie: "9",
                  safari: "10",
                  edge: "17",
                },
              },
            ],
          ],
        },
      },
    ],
  },
  performance: { hints: false },
  externals: {
    jquery: "jQuery",
    axios: "axios",
    dayjs: "dayjs",
  },
  plugins: [
    // 执行顺序由上至下
    new CleanWebpackPlugin({
      cleanOnceBeforeBuildPatterns: [publishPath],
    }),
    new webpack.ProvidePlugin({
      //定义全局import
      $: "jquery",
      jQuery: "jquery",
    }),
    new HtmlWebpackPlugin({
      title: "作者页面",
      filename: "pages/author.html",
      template: "./src/pages/author.html",
      hash: false,
      chunks: ["author"],
    }),
    new CopyPlugin({
      patterns: [
        {
          from: "./src/js/office-js",
          to: "js/office-js",
        },
        {
          from: "./src/images/",
          to: "images",
        },
      ],
    }),
  ],
};
```

<h1 align="center">参考文献</h1>

## Production <span id="production"></span>
https://webpack.js.org/guides/production/

## publicPath
https://webpack.js.org/guides/public-path/#root

## DefinePlugin
https://webpack.js.org/plugins/define-plugin/#usage

## Externals
https://webpack.js.org/configuration/externals/#root

## Terser
https://webpack.js.org/plugins/terser-webpack-plugin/
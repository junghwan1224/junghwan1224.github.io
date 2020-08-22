---
layout: post
title: Webpack & Babel
date: 2020-08-22 12:00:00 +0900
category: Deployment
---



## Webpack & Babel 설정



#### Webpack?

- 모듈 번들러(`Module bundler`) 중 하나
- 모듈 번들러?
  - 여러 개의 파일을 하나로 통합시켜주는 라이브러리
  - 왜 모듈 번들러를 쓰는가?
    - 예전의 자바스크립트로 개발할 때에는 여러 개의 파일을 분리하여 작업을 했었다. 그러나 이를 위해 여러 개의 파일을 하나씩 서버에 요청하여 로드하는 것도 비효율적일 뿐만 아니라, 중간에 하나라도 로드가 안됐을 경우 제대로 웹사이트가 제대로 동작하지 않을 수 있다는 문제가 있다.
    - 또한 의존성이 있는 코드 사이에 순서 보장이 어렵고, 파일이 분리되어 있음에도 변수 스코프를 고려해야 하는 상황을 개선하기 위해 등장
- 파일을 하나로 통합하면 초기 로딩 시 매우 느려질 수 있음. 이를 보완하기 위해 코드 스플릿과 같은 개념들을 활용 - 이 부분은 공부가 더 필요하다.



#### Babel?

- 트랜스파일러(`Transpiler`)
- 최신 사양의 자바스크립트 코드를  지원하지 않는 브라우저(e.g. IE)에서도 동작할 수 있도록 ES5 이하의 코드로 변환(트랜스파일링)시켜준다.



### 프로젝트를 진행하면서 작성한 설정 파일

##### 필요 모듈(여러 삽질을 하느라 어떤 것이 필요하고 필요 없는지 잘 모른다.. 정리하면서 다시 추려내겠다.)



> `@babel/core` babel을 사용하기 위해 추가해야 함
>
> `@babel/cli` 터미널에서 babel 명령어를 사용할 때 필요
>
> `babel-loader` babel과 webpack을 이용해 자바스크립트 파일을 트랜스파일링
>
> `css-loader` css 파일을 임포트할 수 있게끔 해준다
>
> `html-webpack-plugin` 번들링의 결과물을 html 파일로 반환
>
> `mini-css-extract-plugin` js 파일에 있는 css 코드를 별도의 css 파일로 변환
>
> `node-sass` sass를 css로 컴파일
>
> `sass-loader` node-sass를 이용해 sass -> css 작업
>
> `style-loader` 변환된 css를 DOM에 삽입(head 태그 안에 style을 넣어줌)
>
> `webpack` 웹팩 사용을 위해 필수로 추가해야 함
>
> `webpack-cli` 터미널에서 webpack 명령어를 사용하기 위해 필요
>
> `webpack-dev-server` 웹팩 개발서버 관련 모듈

```javascript
// webpack.config.js
// 웹팩이 실행될 때 참조하는 설정 파일

const path = require("path");
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  // @babel/polyfill: ES6에서 ES5 이하의 문법으로 트랜스파일링 할 때 대체할 수 없는 문법(e.g. Promise, Array.from...)들을 사용할 수 있게 해주는 모듈 
  entry: ["@babel/polyfill", "./src/index.js"],
  // 번들링된 파일의 저장 경로와 이름 설정
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname + "/build")
  },
  // webpack-dev-server
  // 소스코드를 수정할 때마다 웹팩이 알아서 빌드해주기 위해 설정
  devServer: {
    contentBase: path.resolve("./build"),
    index: "index.html",
    port: 3000,
    disableHostCheck: true
  },
  mode: "none", // development, production 등 해당 모드에 맞는 값 지정
  module: {
    rules: [
      {
        // js 파일 빌드
        test: /\.(js|jsx)$/,
        include: [path.resolve(__dirname, 'src/')],
        exclude: "/node_modules",
        use: {
          loader: "babel-loader",
          options: {
            presets: ['@babel/preset-env'],
            plugins: ['@babel/plugin-proposal-class-properties']
          }
        },
      },
      {
        // html 파일 빌드
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            // minimize: 코드 최적화 옵션
            options: { minimize: true }
          }
        ]
      },
      {
        // css 빌드
        test: /\.css$/,
        // 로더 순서는 오른쪽에서 왼쪽으로
        // css-loader로 파일을 먼저 읽고 MiniCssExtractPlugin.loader로 css 파일을 읽고 빌드된 파일을 반환
        use: [MiniCssExtractPlugin.loader, 'css-loader']
      },
      {
        // sass 빌드
        test: /\.scss$/,
        // 로더 순서, sass-loader -> css-loader -> MiniCssExtractPlugin.loader
        use: [MiniCssExtractPlugin.loader, "css-loader", "sass-loader"]
      },
      {
        test: /\.(png|jpg|jpeg|svg|gif|ico)$/,
        use: ["file-loader"]
      }
    ]
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: './public/index.html', // 읽어들일 파일 경로 및 이름.
      filename: 'index.html', // 출력할 파일명
      favicon: "./public/favicon.ico"
    }),
    new MiniCssExtractPlugin({
      filename: 'style.css'
    })
  ]
};
```



```javascript
// .babelrc
// @babel/preset-env는 바벨 플러그인을 모아둔 것
// @babel/preset-env  @babel/preset-flow  @babel/preset-react  @babel/preset-typescript가 babel에서 제공하는 공식 프리셋이다.
{
  	// presets에 적은거는 적은 모듈을 사용하겠다는 의미
    "presets": ["@babel/preset-env", "@babel/preset-react"],
    "plugins": [
        "@babel/plugin-proposal-class-properties",
        "transform-regenerator"
    ]
}
```



```javascript
// package.json scripts
"scripts": {
  	// webpack.config.js에서 devServer 옵션 실행시키기 위한 명령어
    "start": "webpack-dev-server --hot",
    "build": "webpack -w",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
```



> 웹팩&바벨 기초 세팅은 했지만, 더 나아가 development / production 모드를 분리하고 각각의 환경에 맞는 모듈들을 붙여서 관리를 하는 연습을 해야할 것 같다.
>
> 내용이 어설프거나 부족한 부분은 공부해서 내용 보충할 것


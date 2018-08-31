# react-skeleton-2018

2018년에 Babel7 과 Webpack 4 를 사용해서 리액트 프로젝트를 0부터 만든다면?

차근차근 해봅시다. 어렵지 않아요.

## 1. 디렉토리 생성

```
$ mkdir react-skeleton-2018
$ cd react-skeleton-2018
$ yarn init
```

## 2. 웹팩 설치

```
$ yarn add --dev webpack webpack-cli
```


## 3. babel 관련 모듈 설치
```
$ yarn add --dev @babel/core babel-loader @babel/preset-env @babel/preset-react
```

## 4. .babelrc 파일 생성
```json
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

## 5. webpack.config.js 작성

#### config/webpack.config.js
```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader'
        }
      }
    ]
  }
};
```

## 6. scripts 추가
#### package.json
```
  "scripts": {
    "build": "webpack --mode production --config ./config/webpack.config.js"
  }
```

## 7. 리액트 라이브러리 설치
```
$ yarn add react react-dom
```

## 8. 컴포넌트 만들기
#### src/App.js
```javascript
import React from 'react';

const App = () => {
  return (
    <div>
      <h1>React Skeleton 2018</h1>
      <p>되게 달라졌다...</p>
    </div>
  );
};

export default App;
```

## 9. 엔트리 파일 (index.js) 작성
#### src/index.js
```javascript
import App from './App';
import ReactDOM from 'react-dom';
import React from 'react';

const rootEl = document.getElementById('root');

ReactDOM.render(<App />, rootEl);
```

## 10. html 파일 작성
#### public/index.html
```html
<!DOCTYPE html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>React Skeleton 2018</title>
</head>
<body>
  <div id="root"></div>
</body>
</html>
```

## 11. html-loader 및 HtmlWebpackPlugin 설정
```
$ yarn add --dev html-webpack-plugin html-loader
```

#### config/webpack.config.js
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader'
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader"
          }
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./public/index.html",
      filename: "./index.html"
    })
  ]
};
```

이제 한번 빌드 해보세요

```
$ yarn build
```

## 12. 개발서버 설정

```
$ yarn add --dev webpack-dev-server
```

그리고 start 스크립트 추가

#### package.json
```javascript
  "scripts": {
    "build": "webpack --mode production --config ./config/webpack.config.js",
    "start": "webpack-dev-server --config ./config/webpack.config.js --open --mode development --port 3000"
  }
```

다음 명령어를 입력하면 개발서버가 뜹니다.
```
$ yarn start
```

## 13. CSS 가 쓰고싶다!
```
$ yarn add --dev css-loader style-loader postcss-loader postcss-preset-env cssnano
```

#### postcss.config.js
```javascript
module.exports = {
  plugins: {
    "postcss-preset-env": {},
    cssnano: {}
  }
}
```

#### config/webpack.config.js
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader'
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader"
          }
        ]
      },
      {
        test: /\.css$/,
        use: [
          'style-loader',
          'css-loader',
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./public/index.html",
      filename: "./index.html"
    })
  ]
};
```

#### src/App.css
```css 
.App {
  background: black;
  padding: 1rem;
  color: white;
}
```
#### src/App.js
```javascript
import React from 'react';
import './App.css';

const App = () => {
  return (
    <div className="App">
      <h1>React Skeleton 2018</h1>
      <p>되게 달라졌다...</p>
    </div>
  );
};

export default App;
```

### 14. mini-css-extract-plugin 적용

CSS 를 따로 파일로 추출하고싶다면 이 라이브러리를 사용하세요

이전에 사용하던 extract-text-webpack-plugin 는 webpack v4 에서 작동하지 않아요.

```
yarn add --dev mini-css-extract-plugin
```

#### config/webpack.config.js
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader'
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader"
          }
        ]
      },
      {
        test: /\.css$/,
        use: [
          'style-loader',
          {
            loader: MiniCssExtractPlugin.loader
          },
          { loader: 'css-loader', options: { importLoaders: 1 } },
          'postcss-loader'
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./public/index.html",
      filename: "./index.html"
    }),
    new MiniCssExtractPlugin({
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
  ]
};
```


### 15. 파일을 불러올까?

```
$ yarn add --dev file-loader
```

#### config/webpack.config.js
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader'
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader"
          }
        ]
      },
      {
        test: /\.css$/,
        use: [
          'style-loader',
          {
            loader: MiniCssExtractPlugin.loader
          },
          { loader: 'css-loader', options: { importLoaders: 1 } },
          'postcss-loader'
        ]
      },
      {
        test: /\.(png|jpg|gif|svg)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
            }
          }
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./public/index.html",
      filename: "./index.html"
    }),
    new MiniCssExtractPlugin({
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
  ]
};
```

src 디렉토리에 이미지 다운로드 

```
$ cd src
$ curl https://avatars0.githubusercontent.com/u/17202261\?s\=460\&v\=4 -o vp.png
```

#### src/App.js
```javascript
import React from 'react';
import vp from './vp.png';
import './App.css';

const App = () => {
  return (
    <div className="App">
      <h1>React Skeleton 2018</h1>
      <p>되게 달라졌다...</p>
      <img src={vp} />
    </div>
  );
};

export default App;
```
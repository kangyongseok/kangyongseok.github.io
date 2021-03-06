---
title: "인프런 - 프론트엔드 개발환경의 이해와 실습-06"
categories: 
  - study
tags: 
    - lecture
    - online
    - inflearn
    - front
    - webpack
    - npm
    - study
    - plugin
    - 웹팩 심화
toc: true
toc_sticky: true
comments:  true
---

## 웹팩 심화


### 웹팩 개발 서버
프론트엔드 개발환경에서 개발용 서버를 제공하는것이 `webpack-dev-derver`

``` javascript
$ npm i -D webpack-dev-server

// package.json
{
    ...
    "scripts": {
        "start": "webpack-dev-server --progress"
    }
}

$ npm start
```

### 기본설정
웹팩 설정파일의 devServer 객체에 개발 서버 옵션을 설정할 수 있다.
``` javascript
// webpack.config.js
module.exports = {
    devServer: {
        contentBase: path.join(__dirname, "dist"), 
        publicPath: "/",
        host: "dev.domain.com",
        overlay: true,
        port: 8081,
        stats: "errors-only",
        historyApiFallback: true,
    }
}
```
`contentBase`: 정적파일을 제공하는 경로. 기본값은 웹팩 아웃풋이다.  
`publicPath`: 브라우저를 통해 접근하는 경로  
`host`: 개발환경에서 도메인을 맞추어야 하는 상황에서 사용  
`overlay`: 빌드시 에러나 경고를 브라우저 화면에 표시  
`port`: 개발 서버 포트 번호를 설정. 기본값은 8080  
`stats`: 메세지 수준을 설정. none, errors-only, minimal, normal, verbose  
`historyApiFallback`: 히스토리 API 를 사용하는 SPA 개발시 설정. 404가 발생하면 index.html로 리다이렉트 한다.  
  
개발서버 실행시 `--progress` 를 추가하면 빌드 진행을 보여준다.

  

### API 서버연동
서버와 `api` 연결전 목업데이터를 자체적으로 생성해서 개발을 진행 할 수 있다.  

``` javascript
// webpack.config.js
module.exports = {
    ...
    devServer: {
        before: app => {
            app.get('/api/users', (req, res) => {
                res.json([
                    {
                        id: 1,
                        name: "Alice"
                    },
                    {
                        id: 2,
                        name: "kang"
                    }
                ])
            })
        }
    }
}
```

생성한 `api` 데이터를 불러와 사용하기 위해 `axios` 를 설치하여 사용한다.

``` javascript
$ npm i -D axios

// app.js
import axios from 'axios';

document.addEventListener("DOMContentLoaded", async () => {
    const res = await axios.get('/api/users')
    console.log(res)
})

```

만약 만들어야하는 목업 데이터가 여러개라면 아래의 방법을 사용할 수 있다.

``` javascript
$ npm i -D connect-api-mocker

// mocks/api/users/GET.json
[
    {
        "id": 1,
        "name": "kang"
    },
    {
        "id": 2,
        "name": "yong"
    }
]

// webpack.config.js
const apimocker = require('connect-api-mocker')
module.exports = {
    ...
    devServer: {
        before: app => {
           app.use(apiMocker('/api', 'mocks/api'))
        }
    }
}

```

### 핫 모듈 리플레이스먼트
변경된 모듈에 대해서만 자동으로 수정해서 화면에 출력하여 결과를 보여줌

``` javascript
// webpack.config.js
devServer: {
    hot: true,
}

// src/app.js
import form from './form';
import result from './result';

let resultEl;
let formEl;

document.addEventListener("DOMContentLoaded", async () => {
    formEl = document.creatteElement("div");
    formEl.innerHTML = await form.render();
    document.body.appendChild(formEl);

    resultEl = document.creatteElement("div");
    resultEl.innerHTML = await result.render();
    document.body.appendChild(resultEl);
});


if (module.hot) {
    console.log("핫 모듈 켜짐");

    module.hot.accept("./result", async () => {
        console.log("result 모듈 변경됨")
        resultEl.innderHTML = await result.render();
    })

    module.hot.accept("./form", async () => {
        formEl.innderHTML = await form.render();
    })
}
```

## 최적화
코드가 많아지면 번들링된 결과물도 커진다. 이는 브라우저의 성능에 영향을 줄 수 있으므로 최적화가 필요하다.  


#### mode 로 최적화
``` javascript
// webpack.config.js
const mode = process.env.NODE_ENV || 'development'

module.exports = {
    mode,
    ....
}
```

### optimazation 속성으로 최적화
`HtmlWebpackPlugin` 이 `html` 을 압축시킨것처럼 `css` 파일도 빈칸을 없애는 압축을 하는것이 가능하다.
``` javascript
$ npm i -D optimize-css-assets-webpack-plugin

// webpack.config.js
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
module.exports = {
    optimization: {
        minimizer: mode === 'production' ? [
            new OptimizeCSSAssetsPlugin(),
        ] : [],
    }
}
```

### TerserWebpackPLugin
자바스크립트코드를 난독화하고 debugger 구문을 제거한다. 이 기본옵션 외에도 console.log 를 제거해주는 옵션도 있다.

``` javascript
$ npm i -D terser-webpack-plugin

// webpack.config.js
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
    optimization: {
        minimizer: mode === 'production' ? [
            new OptimizeCSSAssetsPlugin(),
            new TerserPlugin({
                terserOptions: {
                    compress: {
                        drop_console: true, // 콘솔로그 제거
                    }
                }
            }),
        ] : [],
    }
}
```


### 코드 스플리팅

### 다이나믹 임포트

### externals
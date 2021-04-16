# Babel(Transpiler) + Webpack(Module bundler)를 이용하여 ES6+/ES.NEXT 개발환경 구축하기

## Babel + Webpack을 이용하여 개발환경 구축하는 이유

- 크롬, 사파리, 파이어폭스, 엣지 같은 에버그린 브라우저`evergreen browser`의 ES6 지원율은 약 98%로 거의 대부분의 ES6 사양을 지원한다. 하지만 IE 11의 ES6 지원율은 11%이다. 그리고 매년 새롭게 도입되는 ES6 이상의 버전(ES6+)과 제안 단계에 있는 ES 제안 사양(ES.NEXT)은 브라우저에 따라 지원율이 제각각이다.
- 따라서 최신 ECMAScript 사양을 사용하여 프로젝트를 진행하려면 최신 사양으로 작성된 코드를 경우에 따라 구형 브라우저에서 문제 없이 동작시키기 위한 개발 환경을 구축하는 것이 필요하다.
- 또한 대부분의 프로젝트가 모듈을 사용하므로 모듈 로더도 필요하다. ES6 모듈(ESM)은 대부분의 모든 브라우저에서 사용할 수 있다. 하지만 다음과 같은 이유로 아직까지는 ESM보다는 별도의 모듈 로더를 사용하는 것이 일반적이다.
  - IE를 포함한 구형 브라우저는 ESM을 지원하지 않는다.
  - ESM을 사용하더라도 트랜스파일링이나 번들링이 필요한 것은 변함이 없다.
  - ESM이 아직 지원하지 않는 기능(bare import 등)이 있고 점차 해결되고는 있지만 아직 몇 가지 이슈가 존재한다.


## Version

- Node laptop: 12.13 / office: 8.12.0
- npm laptop: 6.12.0 / office: 6.4.1

- Package/Plugin - Package Name - Version

1. Babel - `@babel/cli` - 7.10.3
2. Babel - `@babel/core` - 7.10.3`
3. Babel preset - `@babel/preset-env` - 7.10.3
4. Babel plugin - `@babel/plugin-proposal-class-properties` - 7.10.1
5. Webpack - `webpack` - 4.43.0
6. Webpack - `webpack-cli` - 3.3.12
7. webpack plugin - `babel-loader` - 8.1.0

## Babel 설치

- ES6 이상의 표현식을 지원하지 않는 구형 브라우저를 위해 ES5 사양으로 변환할 수 있다. Babel을 사용하여 ES5 사양의 소스코드로 변환하도록 개발 환경을 구축해 보자.

```bash
> npm i @babel/cli@7.10.3 @babel/core@7.10.3 --save-dev
```

- 설치 후 package.json (일부 생략)

```json
{
  "name": "deepdive-webpack",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  // ...
  "devDependencies": {
    "@babel/cli": "^7.10.3",
    "@babel/core": "^7.10.3"
  }
}
```

## Babel 프리셋 설치와 babel.config.json 설정

- Babel을 사용하려면 `@babel/preset-env`를 설치해야 한다. `@babel/preset-env`은 함께 사용되어야 하는 Babel 플러그인들을 모아 둔 것으로 Babel 프리셋이라 부른다. Babel이 공식적으로 제공하는 프리셋은 다음과 같다.
  - @babel/preset-env
  - @babel/preset-flow
  - @babel/preset-react
  - @babel/preset-typescript
- @babel/preset-env는 필요한 플러그인들을 프로젝트 지원 환경에 맞춰 동적으로 결정해 준다. 프로젝트 지원 환경은 Browserslist 형식으로 `.browserslistrc` 파일에 상세히 설정할 수 있다. 만약 프로젝트 지원 환경 작업을 생략하면 기본값으로 설정된다.
- 일단은 기본 설정으로 진행하자. 기본 설정은 모든 ES6+/ES.NEXT 사양의 소스코드를 변환한다.

```bash
> npm i --save-dev @babel/preset-env
```

- 설치 후 package.json 파일

```json
{
  "name": "deepdive-webpack",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  // ...
  "devDependencies": {
    "@babel/cli": "^7.10.3",
    "@babel/core": "^7.10.3",
    "@babel/preset-env": "^7.13.15"
  }
}
```

- babel.config.json 설정 파일 생성 및 작성

```json
{
    "presets": ["@babel/preset-env"]
}
```

## 트랜스파일링
- Babel CLI 명령어를 매번 입력하는 것은 번거로우므로 npm scripts를 설정한다.
```json
  "scripts": {
    "build": "babel src/js -w -d dist/js"
  },
```

- `-w` : 타겟 폴더에 있는 모든 자바스크립트 파일들의 변경을 감지하여 자동으로 트랜스파일한다.(`--watch` 옵션 축약형)
- `-d` : 트랜스파일링된 결과물이 저장될 폴더를 지정한다. 만약 지정된 폴더가 존재하지 않으면 자동 생ㅅ헝한다. (`--out-dir` 옵션의 축약형)

- 테스트할 ES6+/ES.NEXT 사양의 자바스크립트 파일을 작성
```
/src/js
       `--lib.js
       `--main.js
```
```JS
// lib.js

export const pi = Math.PI; // ES6 모듈

export function power(x, y) {
    return x ** y; // ES7 지수 연산자
}

// ES6 클래스
export class Foo {
    #private = 10 // stage 3: 클래스 필드 정의 제안

    foo() {
        // stage 4: 객체 Reset/Spread 프로퍼티 제안
        const { a, b, ...x } = { ... { a: 1, b: 2 }, c: 3, d: 4 };
        return { a, b, x };
    }

    bar() {
        return this.#private
    }
}
```

```JS
// main.js

import { pi, power, Foo } from './lib';

console.log(pi);
console.log(power(pi, pi));

const f = new Foo();
console.log(f.foo());
console.log(f.bar());
```

- 트랜스파일링 명령어 실행
```bash
> npm run build

> deepdive-webpack@1.0.0 build C:\workspaces\deepdive-webpack
> babel src/js -w -d dist/js

{ SyntaxError: C:\workspaces\deepdive-webpack\src\js\lib.js: Support for the experimental syntax 'classPrivateProperties' isn't currently enabled (9:5):

   7 | // ES6 클래스
   8 | export class Foo {
>  9 |     #private = 10 // stage 3: 클래스 필드 정의 제안
     |     ^
  10 |
  11 |     foo() {
  12 |         // stage 4: 객체 Reset/Spread 프로퍼티 제안
```

- stage 3(candidate) 단계에 있는 private 필드 정의 제안에서 에러 발생했다. 이것은 `@babel/preset-env`가 현재 제안 단계에 있는 사양에 대한 플러그인을 지원하지 않기 때문에 발생한 에러다. 따라서 현재 제안 단계에 있는 사양을 트랜스파일링하려면 별도의 플러그인을 설치해야 한다.

## Babel 플러그인 설치
- 설치가 필요한 Babel 플러그인은 Babel 홈페이지에서 검색할 수 있다.
- Babel 홈페이지에서 `class field`를 검색해보자
- 검색된 플러그인 중에서 public/private 클래스 필드를 지원하는 `@babel/plugin-proposal-class-properties`를 설치하자.
```
npm i --save-dev @babel/plugin-proposal-class-properties@7.10.1
```
- 변경된 `package.json`
```json
// ...
  "devDependencies": {
    "@babel/cli": "^7.10.3",
    "@babel/core": "^7.10.3",
    "@babel/plugin-proposal-class-properties": "^7.10.1",
    "@babel/preset-env": "^7.13.15"
  }
```

- 설치한 플러그인은 `babel.config.json` 설정 파일에 추가해야 한다. `babel.config.json` 설정 파일을 다음과 같이 수정한다.
```json
{
    "presets": ["@babel/preset-env"],
    "plugins": ["@babel/plugin-proposal-class-properties"]
}
```

- 트랜스파일링을 실행해보자.
```
npm run build

> deepdive-webpack@1.0.0 build C:\workspaces\deepdive-webpack
> babel src/js -w -d dist/js

Successfully compiled 2 files with Babel (536ms).
```

- 성공하면 dist/js 폴더가 자동 생성되고 main.js와 lib.js가 저장된다. 트랜스파일링된 main.js를 실행해 보자.
```
> node ./dist/js/main.js

3.141592653589793
36.4621596072079
{ a: 1, b: 2, x: { c: 3, d: 4 } }
10
```

## 브라우저에서 모듈 로딩 테스트
- 위 예제의 모듈 기능은 Node.js 환경에서 동작한 것이고 Babel이 모듈을 트랜스파일링한 것도 Node.js가 기본 지원하는 CommonJS 방식의 모듈 로딩 시스템에 따른 것이다. 다음은 src/js/main.js가 Babel에 의해 트랜스파일링된 결과다.
```JS
"use strict";

var _lib = require("./lib");

console.log(_lib.pi);
console.log((0, _lib.power)(_lib.pi, _lib.pi));
var f = new _lib.Foo();
console.log(f.foo());
console.log(f.bar());
```

- 브라우저는 CommonJS 방식의 require함수를 지원하지 않으므로 브라우저에서 실행하면 에러가 발생한다. 루트 폴더에 `index.html`를 작성하여 트랜스 파일링된 파일을 브라우저에서 실행해보자.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script src="dist/js/lib.js"></script>
    <script src="dist/js/main.js"></script>
</body>
</html>
```
- 위 html 파일을 브라우저에서 실행하면 다음과 같은 에러가 발생한다.
```
Uncaught ReferenceError: exports is not defined
    at lib.js:3
main.js:3 Uncaught ReferenceError: require is not defined
    at main.js:3
```
- 브라우저의 ES6 모듈(ESM)을 사용하도록 Babel을 설정할 수도 있으나 앞서 설명한 바와 같이 ESM을 사용하는 것은 문제가 있다. Webpack을 통해 이러한 문제를 해결해보자.

# Webpack

- Webpack은 의존 관계에 있는 자바스크립트, CSS, 이미지 등의 리소스들을 하나(또는 여러 개)의 파일로 번들링하는 모듈 번들러다. <ins>Webpack을 사용하면 의존 모듈이 하나의 파일로 번들링되므로 별도의 모듈 로더가 필요 없다.</ins> 그리고 여러 개의 자바스크립트 파일을 하나로 번들링하므로 HTML 파일에서 script 태그로 여러 개의 자바스크립트 파일을 로드해야 하는 번거로움도 사라진다.
- Webpack과 Babel을 이용하여 ES6+/ES.NEXT 개발 환경을 구축해보자.
- Webpack이 JS파일은 번들링하기 전에 Babel을 로드하여 ES6+/ES.NEXT 사양의 소스코드를 ES5 사양의 소스코드로 트랜스파일링하는 작업을 실행하도록 설정할 것이다.

## Webpack 설치
- 명령어로 설치한다.
```
npm i --save-dev webpack webpack-cli
```
- 설치 완료 후 package.json 파일
```json
// ...
  "devDependencies": {
    "@babel/cli": "^7.10.3",
    "@babel/core": "^7.10.3",
    "@babel/plugin-proposal-class-properties": "^7.10.1",
    "@babel/preset-env": "^7.13.15",
    "webpack": "^4.43.0",
    "webpack-cli": "^3.3.12"
  }
```

## babel-loader 설치
- Webpack이 모듈을 번들링할 때 Babel을 사용하여 ES6+/ES.NEXT 사영의 소스코드로 트랜스파일링하도록 babel-loader를 설치한다.

```
npm i --save-dev babel-loader
```
- npm scripts를 변경하여 Babel 대신 Webpack을 실행하도록 수정하자.

- 변경 후 package.json 파일
```json
//...
  "scripts": {
    "build": "webpack -w"
  },
// ...
  "devDependencies": {
    "@babel/cli": "^7.10.3",
    "@babel/core": "^7.10.3",
    "@babel/plugin-proposal-class-properties": "^7.10.1",
    "@babel/preset-env": "^7.13.15",
    "babel-loader": "^8.1.0",
    "webpack": "^4.43.0",
    "webpack-cli": "^3.3.12"
  }
```

## webpack.config.js 설정 파일 작성
- `webpack.config.js`는 Webpack이 실행될 때 참조하는 설정 파일이다. 프로젝트 루트 폴더에 `webpack.config.js` 파일을 생성하고 다음과 같이 작성한다.
```JS
const path = require('path');

module.exports = {
    entry: './src/js/main.js',
    output: {
        path: path.resolve(__dirname, 'dist/js'),
        filename: 'bundle.js'
    },
    module: {
        rules: [
            {
                test: /\.js$/,
                include: [
                    path.resolve(__dirname, 'src/js')
                ],
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['@babel/preset-env'],
                        plugins: ['@babel/plugin-proposal-class-properties']
                    }
                }
            }
        ]
    },
    devtool: 'source-map',
    mode: 'development'
};
```
- 이제 Webpack을 실행하여 트랜스파일링 및 번들링을 실행해보자. 트랜스파일링은 Babel이 수행하고 번들링은 Webpack이 수행한다.
```
npm run build

> deepdive-webpack@1.0.0 build C:\workspaces\deepdive-webpack
> webpack -w


webpack is watching the files…

Hash: 88588657f09c137dacd4
Version: webpack 4.43.0
Time: 612ms
Built at: 2021-04-16 10:09:28
        Asset      Size  Chunks                   Chunk Names
    bundle.js  8.86 KiB    main  [emitted]        main
bundle.js.map  5.18 KiB    main  [emitted] [dev]  main
Entrypoint main = bundle.js bundle.js.map
[./src/js/lib.js] 4.02 KiB {main} [built]
[./src/js/main.js] 147 bytes {main} [built]
```

- 실행한 결과, dist/js 폴더에 bundle.js가 생성되었다. 이 파일은 main.js, lib.js 모듈이 하나로 번들링된 결과물이다. index.html을 다음과 같이 수정하고 브라우저에서 실행해보자.
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script src="dist/js/bundle.js"></script>
</body>
</html>
```
- bundle.js가 브라우저에서 문제없이 실행된 것을 확인할 수 있다.
```
3.141592653589793
main.js:4 36.4621596072079
main.js:7 {a: 1, b: 2, x: {…}}
main.js:8 10
```

## babel-polyfill 설치
- Babel을 사용하여 트랜스파일링해도 브라우저가 지원하지 않는 코드가 남아 있을 수 있다. 예를 들어 ES6에서 추가된 Promise, Object.assign, Array.from 등은 ES5 사양으로 트랜스파일링해도 ES5 사양에 대체할 기능이 없기 때문에 트랜스파일링되지 못하고 그대로 남는다.

- main.js 수정하여 Promise, Object.assign, Array.from 등이 어떻게 트랜스파일링되는지 확인해 보자.
```JS
// src/js/main.js

import { pi, power, Foo } from './lib';

console.log(pi);
console.log(power(pi, pi));

const f = new Foo();
console.log(f.foo());
console.log(f.bar());

// polyfill이 필요한 코드
console.log(new Promise((resolve, reject) => {
    setTimeout(() => resolve(1), 100);
}));

console.log(Object.assign({}, { x: 1 }, { y: 2 }));

console.log(Array.from([ 1, 2, 3 ], v => v + v));
```

- `dist/js/bundle.js` 를 확인해보자.

```JS
// 190 line
console.log(f.bar()); // polyfill이 필요한 코드

console.log(new Promise(function (resolve, reject) {
  setTimeout(function () {
    return resolve(1);
  }, 100);
}));
console.log(Object.assign({}, {
  x: 1
}, {
  y: 2
}));
console.log(Array.from([1, 2, 3], function (v) {
  return v + v;
}));

/***/ })

/******/ });
//# sourceMappingURL=bundle.js.map
```

- 이처럼 ES5 사양으로 대체할 수 없는 기능은 트랜스파일링되지 않는다. 따라서 구형 브라우저에서도 Promise 등을 사용하기 위해서는 @babel/polyfill을 설치해야 한다.

```
npm i @babel/polyfill
```

- 설치 완료 후 package.json
```json
// ...
  "devDependencies": {
    "@babel/cli": "^7.10.3",
    "@babel/core": "^7.10.3",
    "@babel/plugin-proposal-class-properties": "^7.10.1",
    "@babel/preset-env": "^7.13.15",
    "babel-loader": "^8.1.0",
    "webpack": "^4.43.0",
    "webpack-cli": "^3.3.12"
  },
  "dependencies": {
    "@babel/polyfill": "^7.12.1"
  }
```

- `@babel-polyfill`은 개발 환경에서만 사용하는 것이 아니라 실제 운영 환경에서도 사용해야 한다. 
- ES6의 import를 사용하는 경우에는 진입점의 선두에서 먼저 폴리필을 로드하도록 한다.
```JS
// src/js/main.js
import "@babel/polyfill"
import { pi, power, Foo } from './lib';

// ...
```
- Webpack을 사용하는 경우에는 위 방법 대신 webpack.config.js 파일의 entry 배열에 폴리필을 추가한다.
```JS
const path = require('path');

module.exports = {
    entry: ['@babel/polyfill', './src/js/main.js'],
// ...
```

- 위와 같이 webpack.config.js 파일을 수정하여 폴리필을 반영해보자. 

```
npm run build

> deepdive-webpack@1.0.0 build C:\workspaces\deepdive-webpack
> webpack -w


webpack is watching the files…

Hash: 014719e54afc76980647
Version: webpack 4.43.0
Time: 1063ms
Built at: 2021-04-16 10:34:16
        Asset     Size  Chunks                   Chunk Names
    bundle.js  409 KiB    main  [emitted]        main
bundle.js.map  325 KiB    main  [emitted] [dev]  main
Entrypoint main = bundle.js bundle.js.map
[0] multi @babel/polyfill ./src/js/main.js 40 bytes {main} [built]
[./src/js/lib.js] 4.02 KiB {main} [built]
[./src/js/main.js] 425 bytes {main} [built]
    + 307 hidden modules
```

- bundle.js를 보면 다음과 같이 폴리필이 추가된 것을 확인할 수 있다.
```JS
/***/ "./node_modules/core-js/es6/index.js":
/*!*******************************************!*\
  !*** ./node_modules/core-js/es6/index.js ***!
  \*******************************************/
/*! no static exports found */
/***/ (function(module, exports, __webpack_require__) {

__webpack_require__(/*! ../modules/es6.symbol */ "./node_modules/core-js/modules/es6.symbol.js");
__webpack_require__(/*! ../modules/es6.object.create */ "./node_modules/core-js/modules/es6.object.create.js");
__webpack_require__(/*! ../modules/es6.object.define-property */ "./node_modules/core-js/modules/es6.object.define-property.js");
__webpack_require__(/*! ../modules/es6.object.define-properties */ "./node_modules/core-js/modules/es6.object.define-properties.js");
__webpack_require__(/*! ../modules/es6.object.get-own-property-descriptor */ "./node_modules/core-js/modules/es6.object.get-own-property-descriptor.js");
__webpack_require__(/*! ../modules/es6.object.get-prototype-of */ "./node_modules/core-js/modules/es6.object.get-prototype-of.js");

// ...
```
- 이제 html을 확인해보면 아래와 같이 정상적으로 나오는 것을 확인할 수 있다.
```
3.141592653589793
36.4621596072079
{a: 1, b: 2, x: {…}}
10
Promise {<pending>}
{x: 1, y: 2}
(3) [2, 4, 6]
```
### END
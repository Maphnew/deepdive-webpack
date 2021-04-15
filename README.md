# Babel(Transpiler) + Webpack(Module bundler)를 이용하여 ES6+/ES.NEXT 개발환경 구축하기

## Version

- Node 12.13
- npm 6.12.0

- Package/Plugin - Package Name - Version

1. Babel - @babel/cli 7.10.3
2. Babel - @babel/core 7.10.3
3. Babel preset - @babel/preset-env - 7.10.3
4. Babel plugin - @babel/plugin-proposal-class-properties - 7.10.1
5. Webpack - webpack - 4.43.0
6. Webpack - webpack-cli - 3.3.12
7. webpack plugin - babel-loader - 8.1.0

## Babel 설치

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

- Babel을 사용하려면 @babel/preset-env를 설치해야 한다. @babel/preset-env은 함께 사용되어야 하는 Babel 플러그인들을 모아 둔 것으로 Babel 프리셋이라 부른다. Babel이 공식적으로 제공하는 프리셋은 다음과 같다.
  - @babel/preset-env
  - @babel/preset-flow
  - @babel/preset-react
  - @babel/preset-typescript
- @babel/preset-env는 필요한 플러그인들을 프로젝트 지원 환경에 맞춰 동적으로 결정해 준다. 프로젝트 지원 환경은 Browserslist 형식으로 .browserslistrc 파일에 상세히 설정할 수 있다. 만약 프로젝트 지원 환경 작업을 생략하면 기본값으로 설정된다.
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

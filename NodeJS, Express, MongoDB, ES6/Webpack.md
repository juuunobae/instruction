# Webpack
- 모던 javascript application의 `static Module bundler`
  - `bundle`: 소프트웨어 및 일부 하드웨어와 함께 동작하는데 필요한 모든 것을 포함하는 Package
- 프론트엔드에서 사용하는 여러 javascript 파일들을 하나의 javascript 파일로 만들어 준다.
- javascript 파일 뿐만 아니라 CSS, HTML, Image 등도 번들링 해준다.

## 사용하는 이유
- SPA 방식을 많이 사용하면서 하나의 html 파일(웹페이지)에 연결된 JS파일이 많아지면서 연관되어 있는 종속성있는 파일들을 하나의 파일로 묶어줘서 관리하기가 편하고 많은 양의 파일로드로 인한 네트워크 병목 현상을 예방할 수 있다.
- 프론트앤드의 최신 자바스크립트 문법을 브라우저가 이해하는 문법으로 변환해주기도 한다.

> 백앤드에서 사용되는 최신 자바스크립트 문법은 node의 babel이 변환해주고, 프론드앤드에서 사용되는 최신 자바스크립트 문법은 webpack에서 변환해준다.

# Install
- `webpack`과 `webpack-cli` 설치하기  
  - `npm install webpack webpack-cli -D` 설치


## Setting
- `webpack.config.js` 파일 생성
  - 최신 js 문법을 사용할 수 없다.
```js

  const path = require('path')

  module.export = {
    mode: "development",
    watch: true,
    entry: {
      main: "./src/client/main.js",
      videoplayer: "./src/client/videoPlayer.js",
    },
    output: {
      filename: "js/[name].js",
      path: path.resolve(__dirname, 'assets'),
      clean: true,
    },
    module: {
      rules: [
        {
          test: /\.js$/,
          use: {
            loader: 'babel-loader', // npm install babel-loader -D 설치
            options: {
              presets: [["@babel/preset-ent", { targets: "defaults" }]],
            },
          },
        },
      ], 
    },
  };

```
- mode
  - 현재의 모드를
    - `development`: 개발모드 = 코드 압축을 하지 안혹
    - `production`: 배포모드 = 자동으로 최적화
- watch: 번들이 되고 프로세스가 종료되지 않고 실행되고 있으면서 변경사항을 바로 반영을 해준다.
- clean: webpack이 재 빌드 되기 전에 output 폴더를 삭제한다.
- entry
  - 웹 자원을 변환하기 위해 필요한 최초의 진입점이자, JS 파일 경로
> 엔트리 포인트는 여러 개가 될 수도 있다.</br>
> 이는 SPA보다는 MPA 형태에 더 적합하다.
- output
  - entry 파일의 작업이 끝난 후에 저장할 결과물 파일의 경로
  - `filename`: 웹팩으로 빌드한 파일이름
    - `[name]`을 사용하면 entry에 작성한 파일과 같은 이름으로 결과물 파일이 생성된다.
  - `path`: 해당 파일의 경로, 절대 경로를 입력해 주어야 한다.
- rules
  - 모듈 작성 방법을 수정할 수 있다.
  - `use`: 사용할 loader를 설정해주는 속성
    - `test`: loader를 적용할 파일을 지정해주는 속성 (일반적으로 정규표현식 사용)
    - `loader`: 웹팩이 웹 애플리케이션을 해석할 때 자바스크립트 파일이 아닌 웹 자원(HTML, CSS, Images, 폰트 등)들을 변환할 수 있도록 도와주는 속성인다.
    - `options`: loader에 대한 옵션

> __dirname: 현재 파일의 전체 경로</br>
> path.resolve(): 입력한 parameters를 이용해 경로를 만들어 주는 메소드

### SCSS-loader
- scss파일은 main.js 파일에 import 해준다.
- `npm install sass-loader` 설치
- `npm install css-loader` 설치
- `npm install mini-css-extract-pluginr` 설치

```js

  const MiniCssExtracPlugin = require('mini-css-extract-plugin')

  module.exports = {
    plugins: [new  MinCssExtracPlugin({
      filename: 'css/styles.css'
    })]
    module: {
      rules: [
        {

        },
        {
          test: /\.scss$/,
          use: [MinCssExtracPlugin.loader, 'css-loader', 'sass-loader'],
        },
      ], 
    },
  }

```
- webpack은 오른쪽에서 왼쪽으로 코드가 실행된다.
- 실행순서
  1. sass-loader
     - scss 파일을 로드하고 css로 컴파일한다.
  2. css-loader
     - css 파일에서 사용한 @import / url(...) 코드를 import / require() 처럼 인식하고 처리한다.
     - css 파일을 읽어 JS string으로 반환하는 역할을 한다.
  3. mini-css-extract-plugin-loader
     - css-loader가 읽어들인 css를 빌드한 후 별도의 css 파일을 생성해 준다.

## Run
- webpack의 설정이 끝나면 실행시켜 주어야 한다..
- 실행을 위해 `package.json`의 `script`에 실행 명령어를 설정해준다.
```js

  // package.json

  "script": {
    "dev:assets": "webpack --config webpack.config.js",
  }

//--------------------------------------------------------------------

  "script": {
    "dev:assets": "webpack",
  }

```
> 아래과 같은 코드로 작성해도 된다.</br>
> webpack이 실행되면 webpack.config.js 파일을 제일 먼저 찾기 때문이다.

- 브라우저가 `/assets`폴더에 접근할 수 있도록 static을 설정해준다.
```js

  // server.js

  app.use('/assets', express.static('assets'));

```

- template에 script 코드로 연결할 수 있다.
```pug

  //- base.pug
  head
    link(rel='stylesheet', href='/assets/css/styles.css')

  script(src='/assets/js/main.js/')

```

> 빌드된 코드가 저장된 폴더는 .gitignore 파일에 추가해준다.

### nodemon setting 
- webpack.config.js 파일이나 프론트앤드 코드 파일들을 저장할 때 마다 서버가 새로 실행되지 않게 설정
- `nodemon.js` 파일 생성
```js

  // nodemon.js

  {
    "ignore": ["webpack.config.js", "src/client/*", "assets/*"],
    "exec": "babel-node src/init.js"
  }

```
- `package.json`의 스크립트를 변경해준다.
```js

  {
    "script": {
      "dev:server": "nodemon"
    }
  }
```
> nodemon은 nodemon.json 파일을 제일 먼저 찾아 실행할 것이다.
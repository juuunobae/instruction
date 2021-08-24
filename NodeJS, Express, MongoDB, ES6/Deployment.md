# Setting
## backend build
- babel-node는 개발할 때만 사용되는 목적으로 쓰기 때문에 배포할 때는 쓰지 않는다.
  - performance 문제가 있고 속도가 느리다.
- 코드 배포를 위해 `init.js`를 일반적인 js코드로 바꿔야 한다.
- `babel CLI` 사용
  - `npm install --save-dev @babel/core @babel/cli` 설치
- `script` 생성
```js

    // package.json

    "script": {
        "start": "node build/src"
        "build:server": "bable scr -d build"
    }

```
> - `-d` 빌드한 코드를 저장할 디렉토리
> - 한 파일만 실행하면 그 파일이 모든 파일을 실행하는 `nodemon`과 다르게 `babel`은 모든 폴더를 빌드해서 실행해야 된다.
- `npm run build:server`로 코드들을 빌드한다.
> - `build` 폴더를 `.gitignore`에 추가해준다.
- `npm start`로 build 된 코드들을 실행한다.
> start는 기본 명령어라서 run을 안 써도 된다.

## frontend build
- webpack을 
- `script` 생성
```js

    // package.json

    "script": {
        "build:assets": "webpack --mode=production"
    }

```
> - `webpack.config.js`에 mode를 설정하지 않고 명령어로 전달하기 위해 `webpack.config.js`에서 `mode`를 지워준다.
> - `webpack.config.js`에 watch를 설정하지 않고 명령어로 전달하기 위해 `webpack.config.js`에서 `watch`를 지워준다.
>   - `-w`는 watch를 사용하겠다는 것이고, 작성하지 않으면 사용하지 않겠다는 것이다.
>   - `watch`는 `development`모드에서 사용한다.

- `script`를 하나로 묶어주기
```js

    // package.json

    "script": {
        "build": "npm run build:server && npm run build:assets",
        "build:server": "bable scr -d build",
        "build:assets": "webpack --mode=production",
    }

```
> - `npm run build` 명령어를 사용하면 백엔드, 프론트엔드 모두 빌드된다.

# Heroku
- 여러 언어를 지원하는 클라우드 Paas이다
> - `Paas`란
>   - 서비스형 플랫폼(Platform as a Service)이다.
>   - 클라우드 컴퓨팅 서비스 분류 중 하나이고, 앺을 개발하거나 구현할 때, 관련 인프라를 만들고 유지보수하는 복잡함 없이 앱을 개발, 실행, 관리할 수 있게 하는 플랫폼을 제공

- 간단하게 무료로 호스팅 할 수 있도록 해주는 서비스이다.

## setting
- [Heroku 계정을 생성](https://www.heroku.com/)하고 dashboard에 새 [app을 생성](https://dashboard.heroku.com/new-app)해준다.
- 서버를 업로드하는데 두 가지 방법이 있다.
- Heroku는 git history를 기반으로 작동한다.
  - 코드 수정을 했으면 commit을 해야한다.
### 1. GitHub

### 2. Heroku Git
- Heroku에 로그인 하고 `Heroku CLI` [설치](https://devcenter.heroku.com/articles/heroku-cli)
  - `heroku login` 몀령어로 설치가 됐는지 확인할 수 있다.
- 프로젝트 폴더로 이동
  - git repository가 필요하기 때문에 git init을 한다.
  - github에 업로드를 했으면 할 필요없다.
  - git에서 add, commit 등을 하고 push를 Heroku에 할 수 있다.
- `heroku git:remote -a [app name]` 실행
- `heroku logs --tail`
  - 실시간으로 서버나 heroku의 로그를 보여준다.
- `git push heroku master` 서버 배포
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
- 프로젝트가 저장되어 있는 계정을 선택하고 프로젝트 app을 검색한다.
- 프로젝트를 Connect 해준다.
- `Disable Automatic deploy`를 눌러 자동배포를 활성화시켜준다.
  - git push origin master를 하면 배포된다.

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

# MongoDB Atlas
- [계정 생성](https://account.mongodb.com/account/login?signedOut=true) 후 로그인
- [프로젝트 생성](https://cloud.mongodb.com/v2#/org/60117597a4ea8855e33fc4e5/projects/create)
- `connect` 클릭
  - `Allow Access from Anywhere` 클릭
  - username과 password 입력 후 connection .. 버튼 클릭
  - `Connect your application` 클릭
  - database url을 얻는다.
    - <password>에 설저했던 password로 바꾸면 된다.
  - heroku app의 [setting](https://dashboard.heroku.com/apps/juntube/resources)으로 이동
    - `Reveal Config Vars` 클릭
    - `key = DB_URL`, `value = [databaseurl]` 입력
    - `key = COOKIE_SECRET`, `value = dfajfkleofjo` 입력
> heroku에서 env 변수는 웹에서 설정해주어야 한다.
- `init.js` PORT 변경
  - `const PORT = process.env.PORT || 4000`
  - heroku에서 PORT를 준다.

# Github Login
- heroku app에서 변수를 추가해준다.
  - `key = GH_CLIENT`, `value = []` 입력
  - `key = GH_SECRET`, `value = []` 입력
- github app에서 callback url을 변경해준다.
  - `https://[app name].herokuapp.com/users/github/finish`

# AWS S3(Amazon Simple Storage Service)
- 인터넷 스토리지 서비스이다.
- 파일들을 서버에 저장하지 않고 AWS S3에 저장한다.
  - heroku는 서버를 재배포하게 하면 새 서버를 만드는데, 파일들이 서버에 저장되게 되면 서버가 새로 만들어지면서 그 파일들이 날아간다.

## 환경 설정
- [Amazon 계정 생성](https://portal.aws.amazon.com/billing/signup?nc2=h_ct&src=header_signup&redirect_url=https%3A%2F%2Faws.amazon.com%2Fregistration-confirmation&language=ko_kr#/start)
- [AWS S3]()로 이동
- [Create bucket]() 클릭
  - `Bucket name`은 유일해야한다.
  - `Block all public access` 중 마지막 두개 체크
    - 모든 publick 접근을 막는다.
  - bucket 생성
- API Key 생성
  - [IAM]()으로 이동
  - [users]()로 이동
    - username 작성
    - Programmatic access 체크
    - `next:permissions` 클릭
    - s3FullAccess 체크
      - 파일에 대해서 s3에서 할 수 있는 모든 권한을 준다.
    - `next:Tags` `next:Review` 클릭
    - `create user` 클릭
    - `access key`와 `secret access key를 복사한다.
      - `.env`에 `AWS_ID`, `AWS_SECRET`을 만들어 저장
      - heroku app에 변수로 저장


## Multer S3
- 파일을 AWS S3에 업로드 할 수 있게 해주는 패키지 
- `npm install --save multer-s3` 설치
- `npm install aws-sdk` 설치
```js

  // middlewares.js

  import multerS3 from 'multer-s3';
  import aws from 'aws-sdk';

  const s3 = new aws.S3({
    credentials: {
      accessKeyId: process.env.AWS_ID,
      secretAccessKey: process.env.AWS_SECRET,
    }
  })

  const multerUploader = multerS3({
    s3: s3,
    bucket: '[bucket name]',
    acl: 'public-read',
  })

  export const avatarUpload = multer({
    storage: multerUploader
  })

```
- `acl`: object의 권한
  - 누구에게 파일을 보여줄것인가

### file
- req.file 객체의 key가 바꼈기 때문에 수정해주어야 한다.
```js

  const { file } = req;

  // file.path 경로 값이 있던 key 변경 -> 

  file.location

```

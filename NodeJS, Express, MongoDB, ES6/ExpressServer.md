# Express Server
- 우선 src 폴더를 만들어 코드와 로직의 파일들을 src 폴더에 저장한다.
- 실행시킬 파일이름이 index.js일 필요는 없다. server.js로 바꾼다.

## 서버 만들기
- **express** 모듈을 사용해 **server**를 만들어준다.
```js

    // scr/server.js

    // express 모듈 import
    import express from 'express';

    // 포트를 변수에 대입
    const PORT = 4000;

    // express에서 반환된 application객체를 app 변수에 저장
    const app = express();

    // server 실행이 성공하면 실행 될 콜백함수
    const handleListening = () => console.log(`Server listening on port http://localhost:${PORT}`);

    // 외부에서 서버가 실행될 때 허용할 포트 설정, 접속에 성공하면 실행 될 콜백함수
    app.listen(PORT, handleListening);
```

## GET
- GET은 http 메소드이고, 사용자가 url에 데이터를 담아 서버에게 요청하는 것이다.
- 브라우저 주소창에 url을 치고 들어가는 것은 브라우저가 서버에게 페이지와 데이터를 request 하는 것과 같다. 누군가가 해당 경로(**path**)로 get request를 보내면 서버는 `.get(path, callback)`메소드의 콜백함수를 실행시키고, 콜백함수는 return으로 응답을 해줘야 한다.
```js

    // '/' = root 경로
    app.get('/', (req, res) => {
        return res.end();

        // res 객체에는 여러가지 메소드가 있다.
    });
    // get 메소드가 실행되면 콜백함수는 express로 부터 req, res 인자를 전달 받는다.
    // 브라우저에서 request를 받으면 response를 return 해주어 응답해준다.

```
- **`Request Object`**: [Request(요청)](https://expressjs.com/ko/4x/api.html#req)에 대한 정보가 담긴 객체
- **`Response Object`**: [Response(응답)](https://expressjs.com/ko/4x/api.html#res)에 대한 정보가 담긴 객체
  
> get 메소드의 path가 **Router**이고, callback이 **Controller**라고 생각하면 된다.

## POST
- POST는 http 메소드이고, 사용자가 파일을 보내거나 database에 있는 값을 바꾸는 무언가를 보낼 때 사용한다.
- Login할 때도 사용

```pug

  //- 

  form(method='POST') <!-- action = 데이터를1 보낼 경로-->
    input(name='name', placeholder='Name', type='text') <!-- name 속성은 req.body에 key값이 된다.-->
    input(vlaue='Save', type='submit')

```
- 타입이 submit인 input을 누르면 현재의 url로 post 요청을 보낸다.
  - 요청을 보내는 url을 바꾸고 싶으면 form의 action 속성으로 지정해 줄 수 있다.
- `form`으로 부터 contorller로 넘어온 정보는 `req.body`에 담겨 있다.
- 하지만 express는 form을 처리하지 못한다. 그러기 위해서는 `.urlencoded`메소드를 이용한다.

### .urlencoded
- 시용자의 요청에 의해 request.boby에 들어오는 데이터를 urlencoded 데이터로 파싱한다.
- 여러가지 옵션을 사용할 수 있다.
```js

  // server.js

  app.use(express.urlencoded({ extended: true }));

```
- `extended` 옵션은 중첩된 객체를 허용할지 말지 정하는 것이다. 허용은 true

- post메소드로 데이터를 처리해준다.
```js

  app.post('/', (req, res) => {
    const { name } = req.body;
    const { id }  = req.params;
    return res.redirect('/')
  })

```

## route
- `.route()`메소드를 이용하면 하나의 경로에 get, post request를 모두 처리해야 할 때 더 짧은 코드로 작성할 수 있다.
```js

   app.route('/').get(getController).post(postController)
   // 사용자가 get request를 보내면 get 메소드가 실행될 것이고, post request를 보내면 post 메소드가 실행될 것이다.

```

### .all()
- `.route`메소드를 사용할 때 `.get()`, `.post()` 모두에 대한 middleware를 추가할 수 있다.
```js

  // router.js

  Router.route('/').all(middleware).get().post();

```

## Middleware
- 사용자의 요청과 서버의 응답 사이에 존재하는 소프트웨어이다.
- handler는 controller이고, 모든 controller가 middleware가 될 수 있다.
- controller에는 req, res 말고 하나의 인자가 더 있는데 next이다.
- next() 함수를 호출하면 해당 controller의 다음 controller가 실행된다.
```js

    // middleware는 next 함수를 호출해야지만 다음 controller로 넘어간다.
    // return을 해버리면 다음 controller로 넘어가지 않고 멈출 것이다.
    const middleware = (req, res, next) => {
        console.log('Middleware');
        // next 함수를 호출하지 않으면 다음 controller는 절대 실행되지 않는다.
        next()
    }

    // 마지막 controller는 res를 return해주어야 한다.
    const handleHome = (req, res) => {
        return res.send('Home')
    }

    // 사용자가 해당 경로로 요쳥을 하면 controller는 순서대로 실행된다. 
    // 다수의 cotroller를 사용할 수 있다.
    app.get('/', middleware, handleHome)

```
- 위 코드처럼 get 메소드안에 **middleware**를 넣으면 그 요청에만 **middleware**가 동작한다.
- 여러개의 get 메소드에 대해 **middleware**를 동작 시키고 싶으면 `global middleware`를 사용한다.

### global middleware `.use()` 메소드
    ```js

        const handleHome = (req, res) => res.send('Home');

        const handleLogin = (req, res) => res.send('Login');
        
        const middleware = (req, res, next) => {
            console.log('middleware');
            next();
        }

        // 위에서 아래로 실행되기 때문에 middleware는 아래에 있는 controller에게만 적용된다.
        // 여러 middleware를 같이 사용할 수 있다. 
        app.use(Middleware)
        app.get('/', handleHome)
        app.get('/login', handleLogin)

    ```

### locals middleware
- template에서 변수를 사용하려면 locals 객체를 이용해 middleware를 만들어준다.
  - locals는 pug에서 접근할 수 있는 객체이고, template에서 전역으로 변수를 사용할 수 있게 해준다.
```js

  // middlewares.js

  export const localasMiddleware = (req, res, next) => {
    // res.locals.[변수명] = [값]
    res.locals.siteName = 'Wetube';
    next();
  })

  // server.js
  
  import { localsMiddleware } from './localasMiddleware'

  app.use(localsMiddleware)
 
```


## Third party middleware
- express가 제공하는 built-in middleware가 아닌 추가로 설치하여 사용해야 하는 미들웨어이다.

### Morgan
- 로그 관리를 위한 미들웨어
- `npm install morgan` 설치
```js

  import morgan from 'morgan';

  // morgan은 여러가지 옵션이 있다.
  const logger = morgan('dev');

  app.use(logger);

```

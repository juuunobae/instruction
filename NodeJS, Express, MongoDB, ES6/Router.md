# Router
- 사용자의 요청 경로(path)를 보고 이 요청을 처리할 수 있는 곳(contorller)으로 기능을 전달해주는 역할
- 어플리케이션 엔드 포인트(URI)의 정의, URI가 사용자 요청에 응답하는 방식을 말한다.
- 카테고리 별로 경로를 그룹화한다.</br></br>
- **`express.Router()`** 사용
```js

  // .Router 메소드로 라우터를 만들어준다.
  const Router = express.Router();
  const videoRouter = express.Router();

  // middleware로 라우터 경로를 설정해준다.
  app.use('/', Rotuer);
  app.use('/videos', videoRouter);

  // controller
  const home = (req, res) => res.send('Home')
  const editVideo = (req, res) => res.send('Edit Video')

  // 사용자가 '/'(root url)로 요청했을 때 응답하는 코드
  Router.get('/', home)

  // 사용자가 '/videos/edit' url로 요청했을 때 응답하는 코드
  userRouter.get('/edit', editVideo)

```
- - `.use()`를 사용해 각 카테고리별 대표 경로를 정의하고, 콜백함수로 연결시켜줄 Router를 적용해준다.
    - 사용자가 어떠한 url로 접근 시 그에 해당하는 router로 연결해주고 그 router에서 그 url에 해당하는 controller를 찾아줄것이다.
    - 위의 코드로 예를 들면 사용자가 /videos/edit라는 url로 접근한다고 했을 때
    - 먼저 서버는 /videos url이 들어오면 app.use의 videoRouter 실행시킨다. 그 다음 videoRouter에서 /edit와 일치하는 editVideo 실행시킨다.

- **controllers**와 **routers** 폴더를 만들어서 router 별로 분리하기
  - 각 카테고리 별로 router 파일과 controller 파일을 만든다.
    ```js
      
      // src/routers/Router.js

      import express from 'express';

      const Router = express.Router();

      Router.get('/', )

      export default Router;

    ```
  - server.js에 router파일을 import 해서 사용한다.  
    ```js

      // src/server.js

      import Router from './routers/Router';

      app.use('/', Router);

    ```

## Dynamic Routing(동적 라우팅)
- Routes의 경로에 특정 값을 넣어 해당 페이지로 이동할 수 있게 하는 것을 말한다.

### 동적 URL
- url에 `:(콜론)`을 사용해 변수로 만들어 주고 사용자가 원하는 값을 넣고 요청하면 데이터를 응답한다.
```js

  // src/routers/userRouter.js

  import express from 'express';

  const videoRouter = express.Router();

  // 해당 router는 /users/:id 와 같다
  videoRouter.get('/:id', video);

  export default videoRouter;

```
- 예를 들어 브라우저에서 /videos/234545 라고 요청이 들어오면 **234545** 이 부분이 `:id`로 인식이 되는 것이고 그에 해당하는 페이지를 응답한다.
- `:id`로 요청 받은 값은 `req.params`로 가져올 수 있다.
  - `{ id: 234545 }` 출력

> 정규표현식을 사용해서 변수에 원하늩 타입만 받을 수 있다.</br>

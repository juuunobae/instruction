# Controller
- 어플리케이션의 로직을 담당
- 사용자 요청이 들어왔을 때 그에 해당 하는 데이터를 응답한다.
- `controllers` 폴더를 만들어 controller 파일들을 저장한다.
```js

  // src/controllers/homeController.js

  export const home = (req, res) => res.send('Home')

```
- default로 export를 하게 되면 하나의 모듈만 할 수 있으므로 각 controller를 export 해준다.
```js
  
  // src/routers/Router.js

  import { home } from '../controllers/homeController';

  const Router = express.Router();

  Router.get('/', home);

```
- 필요한 모듈을 import 해서 사용한다.

## status, sendStatus
- 상태코드를 리턴하는 이유
  - 어떠한 기능이 성공적으로 끝났는지 결과로 알려주기 때문이다.
### status()
- render하기 전에 상태코드를 정해주는 것

### sendStatus
- 상태코드를 보내고 연결을 끝는 것
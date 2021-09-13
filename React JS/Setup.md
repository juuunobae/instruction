# 프로젝트 생성
## create-react-app
- react로 작업하기 위한 환경을 만들어준다.
- `yarn global add npx`  or `npm i npx -g`
- `npx create-react-app [folder name]`
  - npx: Node Package Runner
    - npm으로 전역에 설치하는 패키지들은 잔여 파일들이 남게 되지만 npx로 설치하게 되면 패키지가 실행된 후 스스로 제거가 된다.
    - 한 번만 실행하고 싶을 때 사용한다.

## Delete
- `App.css`
- `App.test.js`
- `index.css`
- `logo.svg`
- `serviceWorker.js`
- `index.js`
    ```js

        import React from 'react';
        import ReactDOM from 'react-dom';
        import App from './App';

        ReactDOM.render(<App />, document.getElementById('root'));

    ```
    - 위 코드만 남기고 지운다.
- `App.js`
    ```js

        import React, { Component } from 'react';
        class App extends Component {
            render() {
                return <div className='App' />;
            }
        }

        export default App;

    ```
    - 위 코드만 남기고 지운다.

## Create
- `.env`
    ```.env

        NODE_PATH=src

    ```
- `yarn add prop-types` 설치
- `src/Components/App.js` App.js 파일 이동
    ```js

        //  index.js

        import App from './Components/App'; // 경로 수정

        import App from 'Components/App'; 
        // 이렇게 경로를 설정해줘도 동작하는데 .env에 작성한 NODE_PATH=src 때문이다.
        // 기본적으로 scr 폴더에 접근하게 만든다.

    ```
- `src/Routes` 폴더 생성
  - `Home.js` 파일 생성
  - `TV.js` 파일 생성
  - `Search.js` 파일 생성
  - `Detail.js` 파일 생성
    ```js

        export default () => '[]';

    ```

## React Router
- react routing package
- `yarn add react-router-dom` 설치
- `Components/Router.js` 생성
```js

    import React from 'react';
    import { HashRouter as Router } from 'react-router-dom';
    import Home from 'Components/Home';

    export default () => {
        <Router>
            <Route path='/' exact components={Home} />
        </Router>
    }

```

# 프로젝트 실행
- `yarn start`


# Container Presenter Pattern
- 리액트 디자인 패턴
- `container`: 데이터 처리
  - data와 state를 가지고 api를 불러와서 모든 로직을 처리
- `presenter`: 데이터 출력
  - 컨테이너가 처리한 데이터를 화면에 보여주는 함수형 컴포넌트

## create
- 각 route 별로 폴더를 만들고, 그 안에 파일을 생성해준다.
- `src/Routes/Home/index.js`
    ```js

        import HomeContainer from './HomeContainer';

        export default HomeContainer;

    ```
- `src/Routes/Home/HomeContainer.js`
    ```js

        import React from 'react';
        import HomePresenter from './HomePresenter';

        export default class extends React.Component{
            state = {
                nowPlaying: null,
                upcoming: null,
                popular: null,
                error: null,
                loading: true,
            }

            render() {
                const { nowPlaying, upcoming, popular, error, loading } = this.props;
                return <HomePresenter nowPlaying={nowPlaying} upcoming={upcoming} popular={popular} error={error} loading={loading} />
            }
        }

    ```
- `src/Routes/Home/HomePresenter.js`;
    ```js

        export default () => 'Home';

    ```
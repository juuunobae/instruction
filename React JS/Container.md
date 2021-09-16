# Container Presenter Pattern
- 리액트 디자인 패턴
- `container`: 데이터 처리
  - data와 state를 가지고 api를 불러와서 모든 로직을 처리
- `presenter`: 데이터 출력
  - 컨테이너가 처리한 데이터를 화면에 보여주는 함수형 컴포넌트

## create
- 각 route 별로 폴더를 만들고, 그 안에 파일을 생성해준다.

### Home
- `src/Routes/Home/index.js`
    ```js

        import HomeContainer from './HomeContainer';

        export default HomeContainer;

    ```
- `src/Routes/Home/HomeContainer.js`
    ```js

        import React from 'react';
        import HomePresenter from './HomePresenter';
        import { moviesApi } from 'api';

        export default class extends React.Component{
            state = {
                nowPlaying: null,
                upcoming: null,
                popular: null,
                error: null,
                loading: true,
            }

            async componentDidMount() {
                try{
                    const { data: { results: nowPlaying } } = await moviesApi.nowPlaying();
                    const { data: { results: upcoming }} = await moviesApi.upcoming();
                    const { data: { results: popular }} = await moviesApi.popular();
                    this.setState({
                        nowPlaying,
                        upcomig,
                        popular,
                    })
                } catch{
                    this.setState({
                        error: "Can't find movie information."
                    })
                } finally{
                    this.setState({
                        loading: false
                    })
                }
            }

            render() {
                const { nowPlaying, upcoming, popular, error, loading } = this.state;
                return <HomePresenter nowPlaying={nowPlaying} upcoming={upcoming} popular={popular} error={error} loading={loading} />
            }
        }

    ```
- `src/Routes/Home/HomePresenter.js`;
    ```js

        export default () => 'Home';

    ```

### TV
- `src/Routes/TV/index.js`
    ```js

        import TVContainer from './TVContainer';

        export default TVContainer;

    ```
- `src/Routes/TV/TVContainer.js`
    ```js

        import React from 'react';
        import TVPresenter from './TVPresenter';
        import { tvApi } from 'api';

        export default class extends React.Component{
            state = {
                topRated: null,
                popular: null,
                airingToday: null,
                error: null,
                loading: true,
            }

            async componentDidMount() {
                try{
                    const { data: { results: topRated } } = await moviesApi.topRated();
                    const { data: { results: popular }} = await moviesApi.popular();
                    const { data: { results: airingToday }} = await moviesApi.airingToday();
                    this.setState({
                        topRated,
                        popular,
                        airingToday,
                    })
                } catch{
                    this.setState({
                        error: "Can't find tv information."
                    })
                } finally{
                    this.setState({
                        loading: false
                    })
                }
            }

            render() {
                const {topRated, popular, airingToday, error, loading } = this.state;
                return <TVPresenter topRated={topRated} popular={popular} airingToday={airingToday} error={error} loading={loading} />
            }
        }

    ```
- `src/Routes/TV/TVPresenter.js`;
    ```js

        export default () => 'TV';

    ```



### Search
- `src/Routes/Search/index.js`
    ```js

        import SearchContainer from './SearchContainer';

        export default SearchContainer;

    ```
- `src/Routes/Search/SearchContainer.js`
    ```js

        import React from 'react';
        import SearchPresenter from './SearchPresenter';
        import { moviesApi, tvApi } from 'api';

        export default class extends React.Component{
            state = {
                movieResults: null,
                tvResults: null,
                searchTerm: '',
                error: null,
                loading: true,
            }

            handleSubmit = () => {
                const { searchTerm } = this.state;
                if(searchTerm !== ''){
                    this.searchByTerm()
                }
            }

            searchByTerm = async () =>  {
                const { searchTerm } = this.state;
                this.setState({ loading: true });
                try{
                    const { data: { results: movieResults }} = await moviesApi.search(searchTerm);
                    const { data: results: tvResults }} = await tvApi.search(searchTerm);
                    this.setState({ movieResults, tvResults})
                }catch{
                    this.setState({ error: "Can't find results" })
                }finally{
                    this.setState({ loading: false })
                }
            }

            render() {
                const { movieResults, tvResults, searchTerm, loading, error } = this.state;
                return <TVPresenter topRated={topRated} popular={popular} airingToday={airingToday} error={error} loading={loading} handleSubmit={handleSubmit} />
            }
        }

    ```
- `src/Routes/Search/SearchPresenter.js`;
    ```js

        export default () => 'Search';

    ```

### Detail
- `Components/Router.js` 생성
```js

    import React from 'react';
    import { BrowserRouter as Router, Route, Redirect, Switch } from 'react-router-dom';
    import Detail from 'Components/Detail';

    export default () => {
        <Router>
            <>
                <Header />
                <Switch>
                    <Route path='/movie/:id'  components={Detail} />
                    <Route path='/show/:id'  components={Detail} />
                </Switch>
            </>
        </Router>
    }

```
# API
- [MovieDB](https://www.themoviedb.org/settings/api?language=ko)에 API를 요청하고 응답받는다.

## axios
- 브라우저, NodeJS를 위한 Promise API를 활용하는 HTTP 비동기 통신 라이브러리이다.
- fetch api와 유사하지만 차이점이 있다.
  - 간단하게 사용할 때는 fetch를 쓰고, 이외의 확장성을 염두해봤을 땐 axios를 쓰면 좋을 거 같다.
- `yarn add axios` or `npm i axios` install
- `src/api.js` 생성
    ```js

        import axios from 'axios';

        const api = axios.create({
            baseURL: 'https://api.themoviedb.org/3/', //10923b261ba94d897ac6b81148314a3f
            params: {
                api_key: '503131560042303c4c598e10b819ed3d',
                language: 'en-US',
            }
        })

        export const moviesApi = {
            nowPlaying: () => api.get('movie/now_playing'),
            upcoming: () => api.get('movie/upcoming'),
            popular: () => api.get('movie/popular'),
            movieDetail: (id) => api.get(`movie/${id}`, {
                params: {
                    append_to_response: 'videos',
                }
            }),
            search: (term) => api.get('search/movie', {
                params: {
                    query: encodeURIComponent(term),
                }
            })
        }

        export const tvApi = {
            topRated: () => api.get('tv/top_rated'),
            popular: () => api.get('tv/popular'),
            airingToday: () => api.get('tv/airing_today'),
            showDetail: (id) => api.get(`movie/${id}`, {
                params: {
                    append_to_response: 'videos',
                }
            }),
            search: (term) => api.get('search/tv', {
                params: {
                    query: encodeURIComponent(term),
                }
            })
        }

    ```
    - `.create`
      - axios 인스턴스를 생성한다.
      - 컴포넌트에서 공통으로 사용할 기본 설정값을 지정해줄 수 있다.
    - `.get`
      - get method 요청으로, params에 매개변수를 포함해 요청할 수도 있다.


# React에서 CSS 적용
- 컴포넌트를 이용하는 이유는 어플리케이션의 부분 부분을 캡슐화하는 것이기 때문에 각 컴포넌트 별로 폴더를 생성하고 그 폴더안에 CSS 파일도 생성해 import 해준다.
- `src/Components/Header/Header.js`
    ```js

        import react from 'react';
        import './Header.css'

        export default () => (
            <header className='nav'>
                <ul>
                    <li>
                        <a href='/'>Movies</a>
                    </li>
                </ul>
            </header>
        )

    ```
- `src/Components/Header/Header.css`
    ```css

        .nav ul {
            display: flex;
        }

    ```
- `src/Components/Header/index.js`
    ```js

        import Header from './Header'

        export default Header

    ```
> 하지만 이렇게 css 파일을 생성하면 global 파일이 되어 다른 컴포넌트에서 class명을 중복으로 사용할 수 없어서 불편할 것이다</br>
> CSS Module을 사용해 global 파일을 local이 되게 만들어 줄 수 있다.

## CSS Module
- `src/Components/Header/Header.module.css`
    ```css

        .navList {
            display: flex;
        }

        .navList:hover {
            color: blue;
        }

    ```
- `src/Components/Header/Header.js`
    ```js

        import react from 'react';
        import styles from './Header.module.css'

        export default () => (
            <header>
                <ul className={styles.navList}>
                    <li>
                        <a href='/'>Movies</a>
                    </li>
                </ul>
            </header>
        )

    ```
- `src/Components/Header/index.js`
    ```js

        import Header from './Header'

        export default Header

    ```

- 이렇게 하면 class명을 다른 컴포넌트에서도 중복으로 사용할 수 있다.
  - 컴포넌트 마다 임의로 class명이 조금 씩 다르게 보여진다.

## Styled-Components
- javascript에서 css를 사용할 수 있게 해주는 스타일링 프레임워크이다.
### 설치
- `yarn add styled-components` install
### 문법
- styled는 리엑트 컴포넌트를 리턴한다.
```js

    const [변수명] = styled.[HTML element]`
        style...
    `;

    const [변수명] = styled(리엑트 컴포넌트)`
        style..
    `;

    export default () => (
        <변수명>component</변수명> 
    )

```
- `src/Components/Header.js`
    ```js

        import react from 'react';
        import styled from 'styled-components'

        const Header = styled.header``;

        const List = styled.ul`
            display: flex;
            &:hover {
                color: blue;
            }
        `;

        const Item = styled.li``;

        const SLink = styled(Link)``;

        export default () => (
            <Header>
                <List>
                    <Item>
                        <SLink to='/'>Movies</SLink>
                    </Item>
                    <Item>
                        <SLink to='/tv'>TV</SLink>
                    </Item>
                </List>
            </Header>
        )
        // Header = header, List = ul, Item = li, SLink = Link component와 같다.

    ```

    > \<Link\>는 \<Router\>안에서만 동작한다.

### Global Styled Components
- style components를 모든 파일에 적용시킬 때 
- `yarn add styled-reset` install
  - 모든 css 기본 설정을 없앤다.
- `src/Components/GlobalStyles.js`
    ```js

        import { createGlobalStyle } from 'styled-componentes';
        import reset from 'styled-reset';

        const globalStyles = createGlobalStyle`
            ${reset};
            a {
                text-decoration: none;
                color: inherit;
            }
            * {
                box-sizing: border-box;
            }
            body {
                ...
            }
        `

        export default globalStyles;

    ```
- `src/Components/App.js`
    ```js

        import GlobalStyles from 'Components/GlobalStyles';

        class App extends Component {
            render() {
                return (
                    <>
                        <GlobalStyles />
                    </>
                )
            }
        }
    
    ``` 

### props 전달
- style component에 props를 전달해 사용할 수 있다.
### withRouter
- 다른 컴포넌트를 감싸는 컴포넌트이고, Router에 대한 정보를 준다
- Route가 아닌 컴포넌트에서 `match`, `location`, `history`에 접근하기 위해 사용한다.
- `src/Components/Header.js`
    ```js

        import React from 'react';
        import { Link, withRouter } from 'react-router-dom';
        import styled from 'styled-components'

        const Header = styled.header``;

        const Item = styled.li`
            border-bottom: 5px solid ${props => props.current ? '#3498db' : 'transparent'}
        `;
            // current props 값이 true이면 border-bottom을 준다.

        const Item = styled.li``;

        const SLink = styled(Link)``;


        // location.pathname은 현재 있는 페이지의 url을 알려준다.
        // pathname과 같은 to를 가지고 있는 Item 컴포넌트는 current가 true일 것이다.
        export default withRouter({location: { pathname }} => (
            <Header>
                <List>
                    <Item current={pathname === '/'}>
                        <SLink to='/'>Movies</SLink>
                    </Item>
                    <Item current={pathname === '/tv'}>
                        <SLink to='/tv'>TV</SLink>
                    </Item>
                    <Item current={pathname === '/search'}>
                        <SLink to='/search'>Search</SLink>
                    </Item>
                </List>
            </Header>
        ))

    ```

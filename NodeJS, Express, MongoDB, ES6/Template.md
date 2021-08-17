# Template
- 템플릿 엔진이란 템플릿을 읽어 엔진의 문법과 설정에 따라서 파일을 HTML 형식으로 변환시키는 모듈이다.
- 엔진 종류로는 pug과 ejs가 있다.
- `npm install pug` 설치
- express 설정을 해주어야한다.
```js

  // scr/server.js

  // view engine으로 pug을 사용하겠다고 알려주는 것이다.
  app.set('view engine', 'pug');

```

## 사용하기
- express는 기본적으로 process.cwd() + /views 폴더에서 템플릿을 찾는다
  - process.cwd() = node.js를 실행하는 파일(package.json)이 있는 폴더  
- /views 폴더를 만들고, 폴더 안에 pug 파일을 작성한다.
- 다른 폴더에 views를 생성하면 설정을 바꿔주어야 한다.
```js

  app.set('views', process.cwd() + '/src/views')

```
- controller에서 `.render()` 메소드를 이용해 불러온다.
```js

  // src/controllers/homeController.js

  export const home = (req, res) => res.render('home');

```

## 문법
- 하위 태그는 공백으로 구분한다.
```pug

   //- src/views/home.pug

  doctype html
  html(lang='ko')
    head
      title Home
    body
      h1 Home
      footer &copy; 2021 
```

### javascript 사용
- `#{}`안에 변수나 javascript를 사용한다. 
  
#### javascript 문법 사용
```pug

  footer &copy; #{new Date.now().getFullYear()} Wetube

```

##### Conditionals
- javascript의 조건문을 사용할 수 있다.
```pug

  header
    nav
      ul
        //- 사용자의 로그인 여부에 따라 다르게 보여주는 nav를 예를 든 것
        if user.loggedIn
          li
            a(href='/logout') Logout
        else
          li
            a(href='/login') Login

```

##### Iteration
- javascript의 반복문을 사용할 수 있다.
```js

  export const home = (req, res) => {
    const arr = [1, 2, 3, 4, 5, 6, 7];
    return res.render('home', { arr });
  }

```
```pug

  //- home.pug

  ul
    each value in arr
      li=value
    //- 1부터 7까지 출력
    else
      li Nothing found.
    //- 배열이 비어있으면 else 문이 실행된다.

```

### mixins
- 같은 형태의 블록이 서로 다른 데이터를 가져야 할 때 사용한다.
```js

  //- homeContorller.js

  export const home = (req, res) => {
    const videos = [
      {
        title: 'a',
        rating: 5,
        views: 46,
      },
      {
        title: 'b',
        rating: 2,
        views: 46,
      },
      {
        title: 'c',
        rating: 0,
        views: 46,
      },
    ]
    return res.render('home', { videos });
  }

```
- mixin 생성
```pug

  //- mixins/people.pug

  mixin videos(video)
    div
      h4=video.title
      h6=video.rating
      h5=video.views

```
- mixin 사용
```pug

  //- home.pug

  include mixins/people

  main
    each video in videos
      +videos(video)

```
- +videos는 mixins 파일의 mixin과 이름이 같아야 한다.

#### express 변수 사용
- controller에서 template으로 변수 넘겨주기
- `.render()`의 두번째 인자로 객체를 넘겨준다.
```js

  // src/controller/homeController.js

  export const home = (req, res) => res.render('home', { pageTitle: 'Home' })

```
- pug에서 변수 받아서 사용하기
```pug

  //- src/views/home.pug
  
  body
      header #{pageTitle}

      header=pageTitle
      //- 태그에 변수 하나만을 사용할 때는 = 으로 사용가능하다.

```

### Partials
- header나 footer같이 여러 페이지에 중복되는 코드를 재사용할 수 있게 해주는 것
- partials 폴더 생성 후 폴더안에 필요한 파일을 작성한다.
```pug

  //- src/views/partials/footer.pug

  footer &copy; #{new Date.now().getFullYear()} 

```
- partials가 필요한 pug 파일에서 `include`로 사용한다.
```pug

  //- src/views/home.pug

  body
    include partials/footer.pug

```

### Layout
- 여러 페이지에서 반복되는 layout 코드를 한 파일에 작성해 다른 페이지에서 확장해서 사용할 수 있다.
- block 키워드로 파일마다 다른 내용을 넣을 수 있게 만들어준다.
  - [block blockName]
```pug

  //- src/views/base.pug

   doctype html
  html(lang='ko')
    head
      title Home
    body
      //- base.pug 파일을 확장해서 사용하는 파일들이 서로 다른 코드를 넣을 수 있게 block을 만든 것
      block content

      include partials/footer.pug
      
    block script
```
- `extends` 키워드로 사용
```pug

  //- scr/views/home.pug

  extends base.pug

  //- 확장한 base.pug의 content block에 코드를 넣는다.
  block content
    h1 Home

  //- script가 필요한 template에만 적용할 수 있다.
  block script
    script(src='#')
```

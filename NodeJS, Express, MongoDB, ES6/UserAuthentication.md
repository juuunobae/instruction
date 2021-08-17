# User Authentication

## **회원가입**

### User Model 생성
```js

  // models/User.js

  import mongoose from 'mongoose';

  const userSchema = new mongoose.Schema({
    // unique는 유일한 값을 의미한다.
    email: { type: String, required: true, unique: true },
    username: { type: String, required: true, unique: true },
    password: { type: String, required: true },
    name: { type: String, required: true },
    location: String
  }) 

  const User = mongoose.model('User', userSchema);

  export default User;

``` 
> schema에 unique를 사용하면 경고가 뜬다.
> 경고를 무시해도 되지만 없애고 싶으면 `mongoose.connect`에 `useCreateIndex: true`를 추가하면 된다.

- join template 
```pug

  //- join.pug

  extends base.pug

  block content
    form(method='POST')
      input(placeholder='Name', name='name', type='text', required)
      input(placeholder='Email', name='email', type='email', required)
      input(placeholder='Username', name='username', type='text', required)
      input(placeholder='Password', name='password', type='password', required)
      input(placeholder='Confirm Password', name='password2', type='password', required)
      input(placeholder='Location', name='location', type='text', required)
      input(type='submin', value='Join')
    hr
    div
      span Already have an accout? 
      a(href='/login') Login &rarr;

```
- join controller
```js

  // controller.js

  import User from '../models/User';

  export const getJoin = (req,r es) =>  {
    return res.render('join', { pageTitle: 'Join' })
  }

  export const postJoin = async(req, res) => {
    const { email, username, password, password2, name, location}
    // exists메소드에 $or operator를 사용해 조건중 하나라도 true이면 true를 return한다.
    const exists = await User.exists({ $or: [{ username }, { email }] })

    // 비밀번호와 확인 비밀번호가 같은지 확인
    if(password !== password2){
      // 에러를 출력하고 있는 걸 브라우저는 모르기 때문에 에러를 render할 때는 status메소드를 사용해 status code를 같이 응답해준다.
      return res.status(400).render('join', { pageTitle: 'Join', errorMessage: 'Password confirmation does not match' })
      // template에 에러메시지 표시를 위한 코드를 작성해준다.
    }

    if(exists){
      return res.status(400).render('join', { pageTitle: 'Join', errorMessage: 'This username/email is already  taken' })
      // template에 에러메시지 표시를 위한 코드를 작성해준다.
    }

    try{
      await User.create({
        email,
        username,
        password,
        name,
        location
      })
      return res.redirect('/login')

    }catch(error) {
      return res.status(400). render('join', {  pageTitle: 'Join', errorMessage: error._message })
    }
  }


```
> 콘솔창에 **DeprecationWarning**경고가 뜨면 오래된 기능을 업데이트 하라는 것이다.

- join routing
```js

  // router.js
  import { getJoin, postJoin } from '../controllers/conroller.js';

  Router.route('/join').get(getJoin).post(postJoin)

```

## password hash
###  bcrypt 사용
- 단방향 암호화를 위해 만들어진 해시 함수
- `npm install bcrypt` 설치
```js

  // models/User.js

  import mongoose from 'mongoose';
  import bcrypt from 'bcrypt';

  const userSchema = new mongoose.Schema({
    // unique는 유일한 값을 의미한다.
    email: { type: String, required: true, unique: true },
    username: { type: String, required: true, unique: true },
    password: { type: String, required: true },
    name: { type: String, required: true },
    location: String
  }) 

  userSchema.pre('save', async function(){
    this.password = await bcrypt.hash(this.password, 5);
  })

  const User = mongoose.model('User', userSchema);

  export default User;
  
```
- .pre 메소드를 사용해 user를 저장하기 전에 password를 해싱해준다.
- bcrypt.hash 함수에 해싱할 데이터를 넣고 몇 번 해싱을 할건지 입력해준다.

## **Login**
- login template
```pug

  //- login.pug

  extends base.pug

  block content
    if (errorMessage)
      span=errorMessage
    form(method='POST')
      input(placeholder='Username', name='username', type='text', required)
      input(placeholder='Password', name='password', type='password', required)
      input(type='submin', value='Login')
    hr
    div
      span Don't have an accout? 
      a(href='/join') Create one now &rarr;

```

- controller
```js

  // controller.js

  import User from '../models/User';
  import bcrypt from 'bcrypt';

  export const getLogin = (req, res) => {
    return res.render('login', { pageTitle: 'Login' })
  }

  export const postLogin = async(req, res) => {
    const { username, password } = req.body;
    const user = await User.findOne({ username });
    if(!user){
      return res.status(400).render('login', { 
        pageTitle: 'Login', 
        errorMessage: 'An account with this username does not exists.' })
    }
    const ok = await bcrypt.compare(password, user.password)
    if(!ok){
      return res.status(400).render('login', { 
        pageTitle: 'Login', 
        errorMessage: 'Wrong password' })
    }
    res.redirect('/');
  }
  
```

- Router 변경
```js

  // router.js

  import { getLogin, postLogin } from '../controllers/conroller.js';

  Router.route('/login').get(getLogin).post(postLogin);

```  

## session & cookie
### cookie
- 사용자의 정보가 웹서버를 통해 사용자의 컴퓨터에 직접 저장되는 정보의 단위
- session ID를 저장한다.
- Domain: 쿠키가 어디에서 왔는지, 어디로 가야하는지 알려주는 것
- expires/max-age: 만료날짜

### session
- 일정 시간동안 같은 사용자(브라우저)로 부터 들어오는 일련의 요구를 하나의 상태로 보고 그 상태를 일정하게 유지시키는 기술
  - 일정시간은 방문자가 웹브라우저를 통해 웹서버에 접속한 시점으로부터 웹브라우저를 종료하여 연결을 끝내는 시점
- 방문자의 요청에 따른 정보를 웹서가 세션 아이디 파일을 만들어 서비스가 돌아가고 있는 웹서버에 저장하는 것이다.
> [Session & Cookie](https://github.com/juuunobae/TIL/blob/main/Computer%20Science/Session%20%26%20Cookie.md)
- `npm install express-session` 설치
  - express에서 session을 처리할 수 있게 해주는 미들웨어
```js

  // server.js

  import session from 'express-session';

  // router보다 먼저 초기화 해준다.
  app.use(session({
    secret: '',
    resave: false,
    saveUninitialized: false,
  }))

```
- secret: 쿠키에 서명, 해당 서버가 쿠키를 발급했다는 걸 인증하기 위함이다.
- resave: 세션을 변경 사항 없이도 저장할 것인가
- saveUninitialized: 세션을 초기화 전에도 저장할 것인가
  - 로그인 된 사용자(브라우저)만 세션을 저장하려면 false
  - 로그인이 되면서 session 객체가 변경이 되는데 그 때만 저장하겠다는 것이다.
    - 객체가 변경되는 곳
      - 로그인 post method controller
      - `req.session.loggedIn = true; req.session.user = user;`

  </br>
- controller
```js

  // controller.js

  export const postLogin = async(req, res) => {
    const { username, password } = req.body;
    const user = await User.findOne({ username });
    if(!user){
      return res.status(400).render('login', { 
        pageTitle: 'Login', 
        errorMessage: 'An account with this username does not exists.' })
    }
    const ok = await bcrypt.compare(password, user.password)
    if(!ok){
      return res.status(400).render('login', { 
        pageTitle: 'Login', 
        errorMessage: 'Wrong password' })
    }

    req.session.loggedIn = true;
    req.session.user = user;
    // req.session.user에 로그인한 user 정보를 넣어주었기 때문에 모든 controller에서 사용할 수 있다.
    
    res.redirect('/');
  }

```
- 방문자가 웹사이트에 방문하면 server에서 session ID를 만들어 브라우저로 보내주고, 브라우저는 쿠키에 session ID만 저장하고 server는 그 session ID와 session data는 서버 session storage에 저장한다.
- 브라우저가 해당 웹사이트의 모든 url에 요청을 보낼 때 마다 session ID를 요청과 함께 보낸다.
  
- 로그인 된 사용자에게만 보여질 template
```pug

  //- base.pug

  if loggedIn
    //- loggedIn을 사용하려면 locals middelware에 req.session.loggedIn을 등록해준다.
    li
      a(href='/logout') Logout
  else
    li 
      a(href='/join') Join
    li
      a(href='/Login') Login

```


### Session Store
- session을 저장하는 곳
- 서버의 session storage는 실제 사용하기 위해 있는 건 아니다.
- 서버가 재시작되면 store도 재시작되기 때문에 브라우저가 가지고 있던 cookie는 유효하지 않게 되어버린다.
- 그렇기 때문에 session을 데이터베이스에 저장해야 한다.
- `npm install connect-mongo` 설치
  - mongoDB의 session store이다.
```js

  // server.js

  import MongoStore from 'connect-mongo';

  app.use(session({
    secret: '',
    resave: true,
    saveUninitialized: true,
    store: MongoStore.create({ mongo: '[mongoDB url]' })
    // store에 session이 저장 될 곳을 설정해줄 수 있다.
  }))

```

### session store update
- session store에 저장되어있는 session 정보를 변경해야 할 때
```js

  const { 
    session: {
      user: { _id }
    },
    body: { name, email, username, location }, 
  } = req


  // 방법 1
  req.session.user = {
    ...req.session.user, // 객체안에 있는 내용을 꺼내주는 것이다.
    // 수정할 값들만 덮어써준다.
    name,
    email,
    username,
    location,
  }

  
  // 방법 2
  
  const updatedUser = await User.findByIdAndUpdata(
    _id, 
    {
      name,
      email,
      username,
      location
    },
    { new: true }, // 업데이트 된 데이터 리턴
  )
  req.session.user = updatedUser

```


## social login
- 모든 social login은 동작 방식이 비슷하다.

### github login
- **동작 순서**
1. 사용자를 github ID를 요청하도록 github로 redirect 시킨다.
2. github는 사용자를 token과 함께 웹사이트로 다시 redirect 시킨다.
3. 받아온 token으로 사용자 정보를 요청하고 받아온다.

### github login 실행
- 우선 `https://github.com/settings/apps`으로 가서 `OAuth Apps`에 들어간다.
- `new OAuth App`클릭 후 작성해준다.
  - callback URL에는 `http://localhost:4000/users/github/finish`라고 작성해준다.
    - github가 token과 사용자를 다시 redirect 시킬 때 callbake url이다.
    - 아무 url이나 작성해도 되지만 사용해야 하는 url이기 때문에 기억할 수 있게 작성한다.
  - app을 만들면 clientID와 client secrets를 준다
</br></br>
  

- 사용자를 github 페이지로 redirect 시키기 위해 login template에 url링크를 만들어준다.
  - parameters를 같이 보내줘야하는데 url이 길어지고 복잡해지기 때문에 로그인 링크를 route 경로로 설정해주고 controller에서 redirect 되게 한다.
```pug

  //- login.pug

  br
  a(href='/users/github/start') Continue with Github &rarr;

```
</br>

- github login router를 만들어준다.
```js

  // router.js

  import { startGithubLogin, finishGithubLogin } from '../controllers/controller.js';

  Router.get('/github/start', startGithubLogin) // 사용자를 github로 redirect 시킬 router
  Router.get('/github/finish', finishGithubLogin) // github에서 login 후 웹사이트로 돌아올 때 사용자를 처리할 router

```

- 사용자를 github로 로그인 시키기 위한 controller
- `.env` <= `CH_CLIENT=client_id` 
- `.env` <= `CH_SECRET=client_secrets` 

- githubID를 요청할 controller
  - configuration parameter로 만든 url로 redirect 시켜준다.
```js

  // controller.js

  export const startGithubLogin = (req, res) => {
    const baseUrl = "https://github.com/login/oauth/authorize"; // 사용자를 github의 로그인 페이지로 redirect 시키는 기본 url
    const config = { // parameters를 정의하는 객체
      client_id: process.env.GH_CLIENT,
      allow_signup: false,
      scope: "read:user user:email",
    }
    const params = new URLSearchParams(config).toString(); // parameters를 url형식의 문자열로 변경
    const finalUrl = `${baseUrl}?${params}`; // 
    return res.redirect(finalUrl);
  }
```
- **parameter**
- config 객체에 담고, URLSearchParams().toString 메소드로 url로 변경해준다.
  - client_id: github에서 OAuth Apps등록할 때 받은 clientId
    - 필수 값
  - scope: github에 저장되어 있는 사용자의 어떤 정보를 공유할지 명시, 공백으로 구분한다.
  - github는 이메일, 비밀번호, 보안 관련된 모든 데이터를 가지고 있어 공유할 수 있다.
    - ex) `user`, `read:user`, `user:email` 등
  - allow_signup: 계정이 없는 사용자들에게 계정 생성을 가능하게 할지의 여부
  </br></br>

- github 웹사이트로 redirect가 되고, `authorize`를 누르면 callback url인 `http://localhost:4000/users/github/finish`로 redirect 되면서 `code(req.query.code)`도 같이 보내준다.
- github에서 받은 `code`를 `access_token`으로 바꿔주어야 한다.
- access_token을 요쳥하는 url로 `parameter`들과 POST Request를 보내야 된다.
- access_token으로 github API를 사용해 user 정보를 얻을 수 있다.
- POST request를 보내기 위해서는 fetch를 사용한다.

> nodeJS에서는 fetch가 동작하지 않기 때문에 모듈을 사용해야 한다.
- `npm install node-fetch` 설치
```js

  // controller.js

  import fetch from 'node-fatch';

  export const finishGithubLogin = async(req, res)  => {
    const baseUrl = "https://github.com/login/oauth/access_token"
    const config = {
      client_id: process.env.GH_CLIENT,
      client_secret: process.env.GH_SECRET,
      code: req.query.code,
    }
    const params = new URLSearchParams(config).toString();
    const finalUrl = `${baseUrl}?${params}`;

    // 응답받은 code로 access token을 얻을 수 있는 요청
    const tokenRequest = awiat (await fetch(finalUrl, {
      method: 'POST',
      headers: {
        Accept: 'application/json',
        // JSON을 return 받기 위해서 headers에 해당 코드를 보내야 한다.
      }
    })).json(); // fetch로 요청을 하면 해당 fetch의 JSON으로 응답이 온다.
    
    // code로 요청해 응답받은 객체안에 access token이 있으면 실행
    if('access_token' in tokenRequest){
      const { access_token } = tokenRequest;
      const apiUrl = "https://api.github.com" // access token을 이용해 github API로 가는 url

      // access token으로 github API URL을 fetch, user 정보를 가지고 온다.
      const userData = await (await fetch(`${apiUrl}/user`, {
        // 인증을 위한 access token을 보내주어야 한다.
        headers: {
          Authorization: `token ${access_token}`, 
        }
      })).json();

      // access token으로 github API URL을 fetch, user의 모든 email 정보를 가지고 온다.
      const emailData = await(await fetch(`${apiUrl}/user/emails`, {
        headers: {
          Authorization: `token ${access_token}`
        },
      
      })).json();

      // 가지고 온 모든 email 객체 중에 find의 조건에 맞는 email만 변수에 저장한다.
      const emailObj = emailData.find(email => email.primary === true && email.verified === true);

      // 찾는 email이 없을 경우 실행
      if(!emailObj) {
        return res.redirect('/login')
      }

      // github email과 동일한 email을 가진 user를 불러와 변수에 저장
      const user = await User.findOne({ email: emailObj.email })

      // github email과 같은 email을 가진 user가 있으면 실행되고 해당 유저를 login 시킨다.
      if(!user){
        // github의 email과 같은 email을 가진 user가 없으면 새로운 user model을 생성해준다.
        user = await User.create({
          avatarUrl: userData.avatar_url,
          name: userData.name,
          username: userData.login,
          email: emailObj.email,
          location:userData.location,
          password: '',
          socialOnly: true
        })
      }

      req.session.loggedIn = true
      req.session.user = user
      return res.redirect('/')

    } else {
      return res.redirect('/login')
    }
  }

```
- **parameter**
- config 객체에 담고, URLSearchParams().toString 메소드로 url로 변경해준다.
  - client_id: github에서 OAuth Apps등록할 때 받은 clientId
    - 필수 값
  - client_secret: 
    - 필수 값
  - code: 
    - 필수 값
  </br></br>

- Schema 수정
```js

  // User.js

  const userSchema = new mongoose.Schema({
    socialOnly: { type: Boolean, default: false }, // 추가
    avatarUrl: String, // 추가
    password: { type: String }, 
    // social login을 하면 password는 필요없기 때문에 required 옵션을 지운다.
});

```

- Login controller 수정
```js

  export const postLogin = async(req, res) => {
    const { username, password } = req.body;
    const user = await User.findOne({ username, socialOnly: false });
    if(!user){
      return res.status(400).render('login', { 
        pageTitle: 'Login', 
        errorMessage: 'An account with this username does not exists.' })
    }
    const ok = await bcrypt.compare(password, user.password)
    if(!ok){
      return res.status(400).render('login', { 
        pageTitle: 'Login', 
        errorMessage: 'Wrong password' })
    }

    req.session.loggedIn = true;
    req.session.user = user;
    // req.session.user에 로그인한 user 정보를 넣어주었기 때문에 모든 controller에서 사용할 수 있다.
    
    res.redirect('/');
  }

```
- socialOnly가 false이면 로그인폼을 이용해서 username과 password로 로그인 할 수 있는 user이고, true이면 social로만 가입했기 때문에 password가 없는 user이다.
- social로만 가입을 했을 시에는 database에 password가 없기 때문에 사용자가 모르고 database에 있는 social email과 같은 email로 일반 로그인을 한다고 했을 때는 password로 로그인을 할 수가 없다.
- 이런 사용자가 username과 password로 로그인을 시도하면 socialOnly가 false이면서 사용자가 입력한 username과 같은 user model을 database에서 찾아보고 model이 존재하면 로그인 시킨다.

## logout
- 페이지를 render할 필요 없이 logout 과정을 거치고 home으로 redirect 시킨다.
- `req.session.destroy()`를 사용해 session을 없앤다.
```js

  // controller.js

  export const logout = (req, res) => {
    req.session.destroy();
    return res.redirect('/');
  }

```
- Router
```js

  // router.js

  import { logout } from '../cotrollers/controller.js';

  Router.get('/logout', logout)

```
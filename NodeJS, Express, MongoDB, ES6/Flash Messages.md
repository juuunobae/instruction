# Flash Message
- 사용자에게 메세지를 남기는 것
- messages라는 locals를 사용하게 해준다.

## express-flash
- `npm install express-flash` 설치
- 보통 redirect하면서 메세지를 보낼 때 사용한다.
- 한번 메세지를 보여주고 나면 캐시에서 지운다. (일회성 메세지)
```js

    // server.js

    import flash from 'express-flash';

    app.use(flash())

```
- flash 미들웨어를 생성하면 `req.flash`라는 메서드를 사용할 수 있게 된다.
- req.flash([메세지 타입], [메세지 내용])

```js

    export const cont = (req, res) => {
        req.flash('error', 'Not authorized')
        return res.redirect('/');
    }

```
- redirect 코드 전에 사용하면 redirect 될 때 메세지를 보낸다.

### template에서 사용하기
```pug

    //- mixins/messages.pug

    mixin message(kind, text)
        div.message(class=kind)
            span=text

```
- mixin으로 만들어 여러 메세지를 보여준다.

```pug

    //- base.pug

    include mixins/message

    if messages.error
        +message('error', message.error)
    if messages.info
        +messages('info', message.info)

```
- template에서 messages 객체로 사용할 수 있다.
# Comment section
-  댓글 기능을 만든다.

>  모든 것은 데이터로부터 시작한다.

## Model
- schemam 생성
- `init.js`에 `Comment`를 `import` 해준다.
```js

    // models/Comment.js

    import mongoose from 'mongoose';

    const commentSchema = new mongoose.Schema({
        text: { type: String, required: true },
        owner: { type: mongoose.Schema.Types.ObjetId, required: true, ref:'User' },
        video: { type: mongoose.Schema.Types.ObjetId, required: true, ref:'Video' },
        createdAt: { type: Date, required: true, default: Date.now },
    })

    const Comment = mongoose.model('Comment', commentSchema);
    export default Comment

```
- 댓글은 비디오 안에 달리는 것이고, 비디오는 여러개의 댓글을 가질 수 있고, user도 여러개의 댓글을 가질 수 있다.
- `models/Video.js`와 `models/User.js`에도 schema filed를 만들어 준다.
  - `comments: [{ type: mongoose.Schema.Types.ObjectId, required: true, ref: 'Comment'}]`
- > 사용자가 비디오에 댓글을 달면 그 댓글은 해당 비디오와 사용자를 가지게 되면서, 사용자와 비디오는 여러 댓글 들을 가질 수 있다.

## 댓글창
- 프론트엔드에 댓글창을 만들어준다.
- template을 생성
```pug

    //- watch.pug

    if loggedIn
        div.video__comments
            form.video__comments-form#commentForm
                textarea(cols='30', rows='10', placeholder="write a nice comment...")
                button Add Comment
    
    block scripts
        script(src='/static/js/commentSection.js')

```
- `webpakc.config.js`의 `entry`에 추가
  - `{ commentSection: './src/client/js/commentSection.js' }`
```js

    // client/js/commentSection.js

    const videoContainer = document.qetElementById('videoContainer')
    const form = document.qetElementById('commentForm')

    const handleSumbit = (e) => {
        e.preventDefault();
        const textarea = form.querySelector('textarea');
        const text = textarea.value;
        const videoId = videoContainer.dataset.id;
        if(text === ''){
            return;
        }
        fetch(`/api/videos/${videoId}/comment`, {
            method: 'POST',
            header: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                text,
            }),
        })
    }

    // form은 로그인 한 사용자에게만 보여주기 때문에 사용자가 로그인을 안 했으면 form이 null이 되어서 오류가 난다.
    // 오류를 막기 위해 form이 있을 때만 이벤트를 생성한다.
    // textarea도 이벤트 함수에서 불러오는 것도 같은 이유이다.
    if(form){
        form.addEventListener('submit', handleSumbit)
    }

    
```
- form안에 있는 버튼에 클릭 이벤트를 주면 js는 그것을 감지하고 form을 제출한다.
  - > form안에 있는 버튼을 클릭하면 form이 제출된다.
  - >   > form이 제출되면 설정한 메서드 방식으로 어떠한 url로 전송된다.
- 굳이 버튼의 클릭 이벤트를 감지해서 form을 제출하는 것 대신 submit 이벤트를 감지하게 한다.
- `body`
  - 실질적인 데이터가 포함
  - `fetch 안의 body`로 보내는 데이터는 controller의 `req.body`에서 사용할 수 있다.
  - fetch에서 오는 데이터를 서버가 이해할 수 있게 해주어야 한다.
    - fetch를 통해서 보내는 데이터는 JSON 데이터인데 브라우저와 서버는 통신할 때 데이터를 String으로 변환시킨다.
    - `body: {...}`는 [object Object]로 변환되어 보내진다.
- `JOSN.stringify()`
  - JSON데이터를 string으로 변환시켜준다.
- `header`
  - 요청의 세부사항 명시
  - `'Content-Type': 'application/json'`
    - express에게 JSON을 보내는 것이라고 알려주는 것

## API router
- 프론트엔드에서 API를 요청할 수 있게 router를 만들어준다.
- API Router 생성
```js

    // routers/apiRouter.js

    apiRouter.post('/videos/:id([0-9a-f]{24})/comment', createComment)

```
- API Controller 생성
```js

    // controllers/videoController.js
    export const createComment = (req, res) => {
        const { body, params  } = req
        res.end();
    }

```
- req.body를 사용하기 위한 미들웨어
```js

    // server.js

    app.use(express.json())

```
- `app.use(express.json())`
  - express를 사용중이라면 JSON.parse()
  - header에 Content-Type이 json이라고 명시된 요청이 들어오면 string으로 변경되었던 body의 데이터들을 다시 JS object로 변환해준다.
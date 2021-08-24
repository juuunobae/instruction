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

## 댓글 작성
- 프론트엔드에 댓글 작성 창을 만들어준다.
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
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                text,
            }),
        })

        textarea.value  = '';
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
- `headers`
  - 요청의 세부사항 명시
  - `'Content-Type': 'application/json'`
    - express에게 JSON을 보내는 것이라고 알려주는 것
- `body`
  - 실질적인 데이터가 포함
  - `fetch 안의 body`로 보내는 데이터는 controller의 `req.body`에서 사용할 수 있다.
  - fetch에서 오는 데이터를 서버가 이해할 수 있게 해주어야 한다.
    - fetch를 통해서 보내는 데이터는 JSON 데이터인데 브라우저와 서버는 통신할 때 데이터를 String으로 변환시킨다.
    - `body: {...}`는 [object Object]로 변환되어 보내진다.
- `JOSN.stringify()`
  - JSON데이터를 string으로 변환시켜준다.

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
    export const createComment = async(req, res) => {
        const { 
            params: { id },
            body: { text },
            session: { user },s 
        } = req
        
        const video = await Video.findById(id);

        if(!video){
            return res.sendStatus(404)
        }

        const comment = await Comment.create({
            text,
            owner: user._id,
            video: id
        })
        video.comments.push(comment._id);
        video.save();
        return res.sendStatus(201); // status code 201 == created
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

## 댓글창
- 사용자가 쓴 댓글을 보여주는 창이다.
- template 생성
```pug

    //- watch.pug

    div.video__comments
        ul
            each comment in video.comments.reverse()
                li.video__comment
                    i.fas.fa-comment
                    span #{comment.text}
                    if  String(comment.owner) === String(loggedInUser._id)
                        span#deleteBtn  ❌

```
- watch constroller 수정
```js

    // videoController.js

    export const watch = async(req, res) => {
        const { id } = req.params;
        const video = await Video.findById(id).populate('comments');
    }

```
- 해당 비디오에 저장되어있는 댓글을 보려면 comments를 populate를 해주어야 한다.

### 실시간 업로드
- 사용자가 댓글을 쓰면 새로고침 되지 않고 실시간으로 업로드 되는 것 같은 효과를 준다.
  - 백엔드 pug에서 생성하는 댓글과 같은 형태의 html을 프론트엔드에서 임시로 만들어 실제로 생성된 것과 같은 효과를 준다.
- fetch 수정
```js

    // client/js/commentSection.js

    const addComment = (text, id) => {
        const videoComments = document.querySelector('.video__comments ul')
        const newComment = document.createElement('li')
        newComment.dataset.id = id;
        newComment.className = 'video__comment';
        const icon  = document.createElement('i');
        icon.className = 'fas fa-comment';
        const span = document.createElement(span)
        span.innerText = ` ${text}`;
        const span2 = document.createElement("span");
        span2.innerText = " ❌";
        span2.addEventListener("click", handleDelete);
        newComment.appendChild(icon);
        newComment.appendChild(span);
        newComment.appendChild(span2);
        videoComments.prepend(newComment)
    }

    const handlSubmit = (e) => {
        const response = await fetch(`/api/videos/${videoId}/comment`, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                text,
            }),
        })

        if(response.status === 201){
            const { newCommentId } = await response.json();
            addComment(text, newCommentId);
        }

        textarea.value = '';
    }


```
- fetch는 리턴값으로 response 객체를 반환한다.

## 댓글 삭제
- 삭제 버튼을 누르면 html에서 삭제되고 데이터베이스에서도 삭제되게 한다.
- 실시간으로 업로드 되는 효과를 주기위해 임시로 생성했던 프론트엔드에서 생성한 html도 바로 삭제할 수 있게 한다.
- 삭제 버튼 이벤트 달기 
```js

    // commentSection.js

    const handleDelete = async (e) => {
        const comment = e.target.parentNode;
        comment.remove();
        const commentId = comment.dataset.id;
        await fetch(`/api/comments/${commentId}`, {
            method: "DELETE",
        });
    };

```
> fetch는 메서드를 DELETE로 보낸다.

- api router 추가
```js

    // apiRouter.js

    apiRouter.delete("/comments/:id([0-9a-f]{24})", deleteComment);

```
> delete로 요청이 들어오기 때문데 delete요청을 처리하는 router 생성

- api controller 추가
```js

    export const deleteComment = async (req, res) => {
        const {
            params: { id },
            session: { user },
        } = req;
        const comment = await Comment.findById(id).populate("video");

        if (!comment === user._id) {
            return res.sendStatus(403);
        }

        await Video.findByIdAndUpdate(
            comment.video._id,
            {
            $pull: { comments: comment._id },
            },
            { safe: true }
        );

        await Comment.findByIdAndDelete(id);

        return res.sendStatus(204);
    };

```
- 데이터베이스에서 댓글도 지워지고, 댓글과 연결된 비디오에서도 댓글 데이터가 지워진다.
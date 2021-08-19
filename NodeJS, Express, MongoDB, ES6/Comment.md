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
        const video = videoContainer.dataset.id;
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
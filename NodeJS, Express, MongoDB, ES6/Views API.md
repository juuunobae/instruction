# API views
- 백앤드에서 views api를 받아와 프론트앤드에서 views를 표시한다.
- 비디오 재생이 끝나면 view를 1 더해준다.
  
## 백엔드에서 api 생성
- `apiRouter.js` 파일 생성
```js

    import express from 'express';
    import

    const apiRouter = express.Router();

    apiRouter.post('/videos/:id/view', registerView)

    export default apiRouter;

```
- Router 연결
```js

    // server.js

    app.user('/api', apiRouter)

```
- api controller
```js

    // videoController.js

    export const registerView = async(req, res) => {
        const { id } = req.params;
        const video = await Video.findById(id)
        if(!video){
            return res.sendStatus(404)
        }
        video.meta.views = video.meta.views + 1
        await video.save();
        return res.sendStatus(200)
    }

```

## 프론트엔드에서 api 요쳥
- 비디오가 끝까지 재생완료되면 실행될 이벤트 생성
- `ended` 메소드
  - 미디어가 끝까지 재생 완료되었을 시 실행
```js

    const handleEnded = (e) => {
        const { id } = videoContainer.dataset;
        fetch(`/api/videos/${id}/view`,  {
            method: 'POST'
        })
    }

    video.addEventListner('ended', handleEnded)

```
- 프론트엔드에서 해당 비디오의 id를 알기 위해 date attribute를 사용한다.
- pug이 랜더링 될 때 데이터를 저장할 수 있게 한다.
- `data-`로 시작하는 이름으로, 어떤 데이터든지 저장할 수 있다.
  - `element.dataset`을 이용해서 프론트엔드 코드에서 사용할 수 있다.
```pug

    //- watch.pug

    div#videoContainer(data-id=video._id)

```
    
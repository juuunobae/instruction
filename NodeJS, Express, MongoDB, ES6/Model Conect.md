# 모델간 연결하기
- `Schema`의 필드를 통해 필요한 model의 `id`로 연결할 수 있다.
  - 유니크하고 랜덤한 데이터이기 때문이다.
```js

    // models/Video.js

    const videoSchema = new mongoose.Schema({
        owner: { type: mongoose.Schema.ObjectId, required: true, ref: 'User'},
    });

    const Video = mongoose.model("Video", videoSchema);

```
- mongoose.Schema.ObjectId: model의 ObjectId를 저장하겠다는 것
- ref: 저장할 ObjectId를 가지고 있는 model을 가리키는 것
- `owner`라는 필드에 model `User`의 ObjectId를 저장하겠다는 것
```js

    // models/User.js

    const userSchema = new mongoose.Schema({
        videos: [{ type: mongoose.Schema.ObjectId, required: true, ref: 'Video'}],
    });

    const User = mongoose.model("User", userSchema);

```
- 데이터를 배열로 여러개로 구성할 수 있다.
- [{type: mongoose.Schema.Types.ObjectId}]

model을 생성할 때 연결할 model의 id도 필요하다.

## populate
- 모델간 연결을 통해 저장했던 ObjectId로 실제 Object로 치환하는 명령어
```js

    // controller.js

    const owner = await Video.findById(id).populate('owner');

```
# Database
- mongoDB와 mongoose를 사용한다.

## mongoDB
- 문서지향 저장소를 제공하는 NoSQL 데이터베이스이다.
- 데이터는 Document로 불리고, 데이터들의 집합을 Collection이라고 한다.
- NodeJS와 호환이 좋기 때문에 NodeJS에서 가장 많이 사용되는 데이터베이스이다.
  
### NoSQL
- 관계형 데이터 모델을 사용하지 않고 SQL을 사용하지 않는 데이터베이스 시스템 또는 데이트 스토어
- 단순한 키와 값의 쌍으로 이루어져있는 데이터베이스

## mongoose
- nodeJS에서 mongoDB와 상호작용하기 위해서 필요한 connector
- mongoose를 사용해 javascript로 서버와 mongoDB를 연결할 수 있다.

### ODM
- Object Document Mapping
- 객체와 문서의 1대1 매칭한다는 뜻
- Object는 자바스크립트의 객체이고, Document는 mongDB의 문서이다, 문서를 DB에서 조회할 때 자바스크립트 객체로 바꿔주는 역할이라고 생각하면 된다.
  
### mongoDB에 연결하기
- 터미널 창에 `npm install mongoose` 설치
- 프로젝트 루트 경로에 `db.js` 파일 생성
  -  mongoDB와 연결 해주는 파일
- 터미널에 `mongo`를 입력했을 때 나오는 mongoDB의 url이 연결에 사용된다.
```js

  // db.js

  import mongoose from 'mongoose';

  // server와 mongoDB의 database와 연결해주는 것
  mongoose.connect(mongoURL/dbName, {
    useNewUrlParser: true,
    useUnifiedTopology: ture,
  });

  const db = mongoose.connection;

  const handleError = (error) => console.log('DB Error', error );
  const handelOpen = () => console.log('✅ Connected to DB');

  // database에 에러가 나면 이 이벤트가 발생한다.
  db.on('error', handelError);

  // database에 연결이 성공하면 이 이벤트가 발생한다.
  db.once('open',  handelOpen)

```
- `server.js`파일에 `db`를 import 해주면 서버를 실행시킬 때 자동으로 실행되고 database에 연결을 시도한다.
```js

  // server.js

  // 함수나 모듈을 import 한 것이 아닌 파일 자체를 import 한 것이다.
  import './db';

```

## model 생성
- database가 model을 생성하기 위해서 데이터들이 어떻게 구성되는지 알려주어야 한다.
- 데이터들의 형식과 형태(key의 타입)을 설정해준다.
- 예를 들어 데이터베이스에 동영상을 저장한다고 할 때 동영상 model을 생성해본다.
  - models 폴더 -> `Video.js` 파일 생성
    ```js
      
      // Video.js

      import mongoose from 'mongoose';
      
      const videoSchema = new mongoose.Schema({
        title: {
          type: String,  
          required: true,
          trim: true,
          maxLangth: 80
        },
        description: {
          type: String,  
          required: true,
          trim: true, // 문자열의 좌우 공백을 없애준다.
          maxLength: 140, // 최대 허용 글자 수 => template input 속성도 설정해준다.
        },
        createAt: { 
          type: Date,  // 데이터 타입 설정
          required: true, // true = 필수 값
          default: Date.now // 기본값
        },
        hashtags: [{type: String, trim: true}],
        meta: {
          views: {
            type: Number, 
            default: 0,
          },
          rating: {
            type: Number, 
            default: 0,
          },
        },
      });

      // model을 생성하는데 model 이름과, 데이터의 형태인 schema로 구성된다.
      const Video = mongoose.model('Video', videoSchema);

      export default Video;

    ```
    - 작성한 스키마를 기준으로 데이터를 DB에 넣기 전에 먼저 검사하고, 스키마에 어긋나는 데이터가 있으면 에러를 발생시킨다.
    - 스키마는 타입뿐만 아니라 여러 옵션을 설정할 수 있다.

  - `server.js`파일에 `Video`를 import 해주면 서버를 실행시킬 때 자동으로 실행되고 database에 연결을 시도한다.
  - model을 미리 컴파일 해야지 사용하고 싶을 때 사용할 수 있다.
    ```js

      // server.js

      import './db';
      // 데이터베이스와 연결이 성공적으로 이루어졌을 때 video model을 인식시킨다.
      import './models/Video';
    
    ```

## model 사용
- controller 파일에 model을 import 해준다.
```js

  // controller.js

  import Video from '../models/Video';

```
- mongoose의 model들은 CRUD operation을 돕는 query 관련 function들을 제공한다.

### CRUD
- Create: 생성
- Read: 읽기
- Update: 수정
- Delete: 삭제

### **Read**
### .find()
- document(model)을 찾는 queries 메소드
- **callback function**이나 **promise** 두가지 사용방법이 있다.</br></br>

#### Callback Function
```js

  // controller.js

  import Video from '../models/Video';

  export const home = (req, res) => {
    // 괄호안에 configuration을 넣어주면 된다.
    Video.find({}, (error, videos) => {
      res.render('home', { videos }); // return문이 없어서 render가 실행된다.
    })
    // 중괄호는 search term라고 하고 비어있어으면 모든 값이라는 뜻이다.
    // callback은 데이터 베이스에서 인자로 error와 docs를 넘겨 받는다.
  };

```
- `Video.find({},` 부분까지 database에서 불러온 후 database에서 반응이 있으면 mongoose는 **error**와 **docs**의 값을 불러올 것이다.
- 그렇기 때문에 render메소드를 callback function안에 넣어준다.
- **callback**은 아무것도 **return**되지 않아야 한다.

#### Promise
```js

  // controller.js

  import Video from '../models/Video';
   
  export const home = async(req, res) => {
    try{
      const videos = await Video.find({});
      return res.render('home', { videos });
    } catch(error) {
      console.log(error)
      return null;
    }
  }

```
- **await**를 사용하면 데이터베이스의 응답을 받을 때 까지 다음 코드가 실행되지 않고 기다렸다가 응답이 있으면 다음 코드가 실행된다.
- **await**는 함수안에서만 사용가능하고 그 함수에 async를 작성해주어야 한다.
- promise를 사용하면 error를 처리해 줄 때 try / catch문을 사용하다.
  - try문을 실행하다 error가 발생하면 catch문을 실행한다.


### .findById()
- id로 document(model)을 찾는 queries 메소드
```js

  // controller.js

  import Video from '../models/Video';

  export const detail = async(req, res) => {
    const { id } = req.params
    const video = await Video.findById(id);
    if(!video){
      return res.render('404', { pageTitle: 'Video not found.' });
    } 
    return res.render('detail', { video });
  }

```
> database에 저장될 때 부여해주는 id는 16진수에 24바이트로 되어있다.</br>
> router에 동적 url을 설정해줄 때 `/:id([0-9a-f]{24})`와 같이 정규표현식을 사용할 수 있다.


### **Create**
### .create()
- document(model)을 데이터베이스에 저장하기 위한 queries 메소드
- `.create`는 save()메소드를 실행시킨다.
- 사용자에게 입력받을 폼
```pug

  //- upload.pug

  form(method='POST')
    input(name='title', placeholder='Title', required, type='text', maxlength=20)
    input(name='description', placeholder='Description', required, type='text', maxlength=140)
    input(name='hashtags', placeholder='Hashtags, separated by comma', required, type='text')
    input(type='submit', value='Save')

```
- 사용자의 입력을 받아 database에 upload하는 controller
```js

  // controller.js

  export const postUpload = async(req, res) => {
    const { title, description, hashtags } = req.body;

    // document(model) 생성
    // const video = new Video({
    //   title,
    //   description,
    //   createdAt: Date.now(),
    //   hashtages: hashtags.split(',').map(word => `#${word}`),
    //   meta: {
    //     views: 0,
    //     rating: 0
    //   },
    // })
    // // 생성 후 저장
    // await video.save();


    // create 메소드를 사용하면 생성 후 바로 저장 된다.

    try {
      await Video.create({
        title,
        description,
        hashtages,
      })
      return res.redirect('/');
    } catch(error) {
      console.log(error)
      // 에러가 발생하면 다시 업로드하는 페이지를 랜더한다.
      return res.render('upload', { pageTitle: error });
    }
  }

```

### **Update**
### .findByIdAndUpdate()
- id로 document(model)을 찾고 해당 document를 수정하는 queries 메소드
```js

  // controller.js

  export const getEdit = async(req, res) => {
    const { id } = req.params;
    const video = await Video.findById(id);
    if(!video){
      return res.render('404', { pageTitle: 'Video not found.' });
    } 
    return res.render('edit', { video });
  }

  export const postEdit = async(req, res) => {
    const { id } = req.params;
    const { title, description, hashtags } = req.body;
    const video = await Video.exists({ _id: id });
    // exists 메소드는 document의 객체를 받는대신 데이터가 존재하면 true를 리턴한다.
    if(!video){
      return res.render('404', { pageTitle: 'Video not found.' });
    } 
    await Video.findByIdAndUpdate(id, {
      title, 
      description,
      hashtags,
    })

    // const updatedUser = await User.findByIdAndUpdata(
    //   _id, 
    //   {
    //     name,
    //     email,
    //     username,
    //     location
    //   },
    //   { new: true },
    // )
    return res.redirect(`/videos/${id}`);
  }

```
- 기본적으로 .findByIdAndUpdata는 updata가 되기 전의 데이터를 리턴해주고, `{ new: true }`를 설정해주면 업데이트된 데이터를 리턴해준다.

### **Delete**
### .findByIdAndDelete()
- id로 document(model)을 찾고 해당 document를 삭제하는 queries 메소드
```js

  export const deleteVideo = async(req, res) => {
    const { id } = req.params
    await Video.findByIdAndDelete(id)
    return res.redirect('/');
  }

```

- 삭제 Router
```js

  //  router.js

  videoRouter.route('/:id/([0-9a-f]{24})/delete').get(deleteVideo)

```

## Middleware
- document에 이벤트가 발생하기 전이나 후에 어떠한 동작을 실행하는 것
- middleware에 this를 바인딩하여 사용할 수 있고, 훅의 종류에 따라 가리키는 것이 다르다.

### .pre()
- 이벤트가 발생하기 전에 실핼되는 middleware
- `schema.pre([hook], [async function])`
```js

  // Video.js

  import mongoose from 'mongoose';
      
  const videoSchema = new mongoose.Schema({
    title: { type: String, required: true, trim: true, maxLangth: 80 },
    description: { type: String, required: true, trim: true, maxLength: 140,  },
    createAt: {  type: Date, required: true, default: Date.now },
    hashtags: [{type: String, trim: true}],
    meta: {
      views: { type: Number, default: 0, },
      rating: { type: Number, default: 0, },
    },
  });

  // 아래 middleware는 document가 save()가 실행되기 전 즉, 저장되기 직전에 실행되는 것이다.
  videoSchema.pre('save', async function() {
    if (this.isModified("hashtags")) {
      this.hashtags = this.hashtags[0].split(',').map(word => word.startsWith('#') ? word : `#${word}`)
    }
  })

  // model이 생성되기 전에 만들어야 한다.

  // model을 생성하는데 model 이름과, 데이터의 형태인 schema로 구성된다.
  const Video = mongoose.model('Video', videoSchema);

  export default Video;

```
> .isModified: 해당 필드의 값이 수정되고 model이 저장되거나 로드되면 true를 return한다.

- **save hook**이 발생하면 async function이 실행되고 save hook의 this는 현재 저장할 document를 가리키는 것이다.
- document를 가리키는 this는 `validate, save, remove, init(synchronous)` hook에서만 바인딩 되기 때문에 다른 미들웨어 함수에서는 다른 방법을 사용해야 한다. 
- `.static()`을 이용해 메소드를 직접 만들어 document를 변경하는 방법이 있다.

### .static()
- queries 메소드들 같이 원하는 함수를 만들 수 있다.
- schema.static([메소드 이름], [함수])
```js

  // Video.js

  videoSchema.static('formatHashtags', function (hashtags) {
    return hashtags[0].split(',').map(word => word.startsWith('#') ? word : `#${word}`);
  })

```
- import를 하지 않아도 사용할 수 있다.
```js

  // controller.js

  export const postEdit = async(req, res) => {
    await Video.findByIdAndUpdate(id, {
      title, 
      description,
      hashtags: Video.formatHashtags(hashtags),
    })
    return res.render('edit', { video });
  }

```

## Search
- Router
```js

  // Router.js

  Router.get('/serach', search)

```
- controller
```js

  // controller.js

  export const search = async(req, res) => {
    const { keyword } = req.query
    let videos = [];
    if(keyword){
      videos = await Video.find({
        title: {
          // 정규표현식을 이용해 검색 설정을 해줄 수 있다.
          // mongoDB에서 제공하는 operator를 통해 find()에 옵션을 줄 수 있다.
          $regex: new RegExp(keyword, 'i') // 정규표현식 객체 생성자 함수를 사용해 정규표현식 사용
        },
      })
    }
    return res.render('search', { pageTitle: 'Search', videos });
  }

```

- template
```pug

  //- search.pug
  extends base.pug
  include mixins/video

  block content
    form(method='GET')
      input(placeholder='Search by title', type='text', name='keyword')
      input(type='submit', value='Search now')

    div
      each video in videos
        +video(video)

```

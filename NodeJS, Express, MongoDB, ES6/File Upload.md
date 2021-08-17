# File Upload
웹에서 파일 업로드는 두가지 방법이 있다.

1. applecation/x-www-urlencoded
2. multipart/form-data
   - 1번의 경우 인코딩으로 인한 성능 이슈가 발생할 수 있으니 2번의 방법으로 전송하는 것이 좋다고 한다.

## multer
파일 업로드를 위해 사용되는 multipart/form-data 를 다루기 위한 middelware이다.

- middleware로 사용할 수 있게 만들어준다.
```js

    // Middlewares.js

    import multer from 'multer';

    export const uploadavatar = multer({
        dest: 'uploads/avatars/', // 사용자가 파일을 보내면 어디에 저장할지 설정
        limits: {
            fileSize: 3000000, // byte 단위이다.
        }
    })

```

- 폼을 멀티파트 폼으로 만들주어야 한다.
```pug

    //- .pug
    
    form(method='POST', enctype='multipart/form-data')
        label(for='avatar') 
        input(type='file', id='avatar', name='avatar', accept='*/image')
    //- 라벨의 for과 file input의 id가 같아야한다.
    //- (accept=*/image)이미지 파일중 모든 확장자

```
> enctype = 파일을 백엔드로 보내기 위해 필요한 encoding type

- 파일 업로드를 하는 router에 추가해준다.
```js

    // router.js

    import { uploadavatar } from ',/middelwares.js';
    
    router.post(uploadavatar.single('avatar'), cont)
    router.post(uploadavatar.fields([
        {name: 'video', maxCount: 1},
        {name: 'thumb', maxCount: 1}
    ]), cont)
    

```
> single은 파일을 하나만 upload 하겠다는 것이고, () 괄호안에는 input의 field name을 넣어준다.
1. 템플릿 인풋에서 오는 하나의 아바타 파일을 가지고 파일을 업로드하고 업로드 폴더에 저장한다.
2. 실행될 컨트롤러에 그 파일의 정보들 전달한다.
3. req에 file이 추가된다 (req.file)

> fields는 인자에 명시된 여러 파일을 전달 받는다. () 괄호안에는 input의 field name을 배열 형태로 넣어준다.
1. 템플릿 인풋에서 오는 여러개의 파일(비디오, 썸네일)을 가지고 파일을 업로드하고 업로드 폴더에 저장한다.
2. 실행될 컨트롤러에 그 파일의 정보들 전달한다.
3. req에 files이 추가된다 (req.files)
   - 각각의 fields들이 배열로 files에 저장된다.</br>


- controller에서
```js

    // controller.js

    export const cont = async(req, res) => {

        const {
            session: {
                user: { _id, avatarUrl }
            },
            file , // single로 파일을 하나 업로드 할 때 
            files: { video, thumb }, // fields로 파일을 여러개 업로드할 때 
        } = req; 

        const user = await User.findByIdUpdate(
            id,
            {
                avataUrl: file ? file.path : avataUrl
            },
            { new: true  }
        )
        
    }

```

- 브라우저가 폴더를 찾을 수 있게 해준다.
```js

    // server.js

    app.use(express.static('uploads'))
    
```
- 브라우저가 서버에 있는 파일에 접근할 수 있도록 해당 폴더로 가는 일종의 route를 만들어준다.
- 브라우저가 /uploads로 시작하는 url로 시작을 하면 uploads 폴더를 보여주는 것
- uploads 폴더는 .ginignore에 추가


# FFmpeg
- 비디오나 오디오 같은 미디어 파일들을 다룰 수 있게 해주는 소프트웨어
- 미디어와 관련된 거의 모든 작업을 할 수 있다.
- 백엔드에서 실행된다. 즉, 서버자원을 사용한다.
- 브라우저가 아닌 컴퓨터에서 작업하는 것과 같은 

# ffmpeg.wasm
- 웹어셈블리로 포팅된 ffmpeg를 js에서 사용할 수 있도록 제공하는 인터페이스 형식의 패키지이다.
- 사용자의 컴퓨터를 사용해서 작업한다.

## install
- `npm install @ffmpeg/ffmpeg @ffmpeg/core` 설치
```js

    import { createFFmpeg, fetchFile } from '@ffmpeg/ffmpeg';

```
> `SharedArrayBuffer is not defined`에러가 발생하면 </br>
> `npm install @ffmpeg/ffmpeg@0.9.7 @ffmpeg/core@0.8.5` 버전으로 다운그레이드 시켜준다.

## use
- `createFFmpeg()`
  - ffmpeg instance를 만들어준다.
  - { log: true } = 콘솔에서 확인하기 위한 코드
- `fetchFile`
  - 해당 url을 fetch 한다.
- `.load()`
  - ffmpeg.wasm을 로드한다.
  - 외부 소프트웨어를 사용하기 때문에 awiat를 사용
- `.FS()`
  - ffmpeg의 가상공간에 파일을 관리한다.
  - (method, fliename, binary data)
  - `writeFile`
    - 파일을 생성한다.
  - `readFiel`
    - 파일을 불러온다.
    - `Uint8Array`값을 리턴한다.
      - unsingned integer이다. = 양의 정수
      - 자바스크립트 방식의 파일
  - `unlink`
    - 파일의 링크를 해제한다.
    - 생성했던 파일을 삭제
- `run()`
  - 사용자의 브라우저에서 명령어가 실행되게 한다.
  - ('옵션', '해당 값', 'output')
- `.buffer`
  - binary data에 접근 할 때 사용한다.
  - ArrayBuffer 반환
    - 미디어 데이터를 나타내는 bytes의 배열
- `new Blob()`
  - 실제 파일인 blob을 만들어준다.
  - ([binary data], { 옵션 } )
- `URL.revokeObjectURL`
  - url 삭제
```js

  // recorder.js

  // 반복되는 값들을 객체로 만들어줘 한 곳에서 관리 할 수 있게 해준다.
  const files = {
    input: 'recording.webm',
    output: 'output.mp4',
    thumb: 'thumbnail.jpg'
  }

  // 반복되는 코드를 함수로 만들어 한 곳에서 관리 할 수 있게 한다. 
  const downloadFile = (fileUrl, fileName) => {
    const a = document.createElement('a')
    a.href = fileUrl;
    a.download = fileName;
    document.body.appendChild(a);
    a.click();
  }

  const handleDownload = () =>  {

    actionBtn.removeEventListener('click', handleDownload);
    actionBtn.innerText = 'Transcoding..';
    actionBtn.disabled = true;


    const ffmpeg = createFFmpeg({ log: true, corePath: 'static/ffmpeg-core.js' });
    await ffmpeg.load();
    ffmpeg.FS("writeFile", files.input, await fetchFile(videoFile))
    
    await ffmpeg.run("-i", files.input, files.output)
    await ffmpeg.run("-i", files.input, "-ss", "00:00:01" "=frames:v", '1', files.thumb)


    const mp4File = ffmpeg.FS('readFile', files.output)
    const thumbFiel = ffmpeg.FS('readFile', files.thumb)

    const mp4Blob = new Blob([mp4File.buffer], { type: 'video/mp4' })
    const thumbBlob = new Blob([thumbFiel], { type: "imgage/jpg"})

    const mp4Url = URL.createObjectUrl(mp4Blob)
    const thumbUrl = URL.createObjectUrl(thumbBlob)

    downloadFile(mp4Url, 'MyRecording.mp4')
    downloadFile(thumbUrl, 'MyThumbnail.jpg')

    ffmpeg.FS('unlink', files.input)
    ffmpeg.FS('unlink', files.output)
    ffmpeg.FS('unlink', files.thumb)

    URL.revokeObjectURL(mp4Url);
    URL.revokeObjectURL(thumbUrl);
    URL.revokeObjectURL(videoFile);

    actionBtn.disabled = false;
    actionBtn.innerText = 'Record Again';
    actionBtn.addEventListener('click', handleStart)
    }

```
> run 명령어</br>
> `-i`: 인풋</br>
> `-r`: 초당 프레임</br>
> `-ss`: 영상의 시간으로 이동</br>
> `-frames:v`: 스크린샷</br>
> 마지막 옵션은 output
>  > output은 파일시스템의 메모리에 만들어진다.

### thumbnail 추가   
- `model/Video.js`
```js

  const videoSchema = new mongoose.Schema({
    thumbUrl: { type: String, required: true }
  })

```

- `views/upload.pug`
```pug

  form
    label(for='thumb') Thumbnail File
    input(type='file', accept='image/*', required, id='thumb', name='thumb' )

```
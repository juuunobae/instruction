# 비디오 녹화
- 녹화가 5초가 지나면 녹화된 비디오를 다운로드 받을 수 있게 한다.
- `/scr/client/js/recorder.js` 파일 생성
- `navigator.mediaDevices`
  - 객체를 통해서 해당 기기에 연결되어 있는 장치들을 불러온다.
- `.getUserMedia(constraints)`
  - 사용자에게 미디어 입력 장치 사용 권한을 요청하며, 사용자가 수락하면 요청한 미디어 종류의 트랙을 포함한 MediaStream을 반환한다.
    - MediaStream
      - 비디오 또는 오디오와 같은 미디어 콘텐츠의 스트림을 나탄낸다.
    - `(constraints)`
      - 요청할 미디어 유형과 각각에 대한 요구사항을 지정하는 객체
      - ex) 해상도, 최소 너비 및 높이 설정 등
  - `video.scrObject`
    - 현재 HTMLMediaElement 객체에서 재생중이거나 재생되었던 미디어를 참조하는 MediaStream 객체를 참조한다. 
  - `MediaRecorder(stream, { option... })`
    - 지정된 mediaStream을 기록하는 새로운 MediaRecorder 객체를 만든다.
    - `(stream)`
      - 기록될 mediaStream
      - `navigator.mediaDevices.getUserMedia()` 또는` <audio> <video> <canvas>` 사용하여 생성된 스트림에서 나올 수 있다.
  - `recorder.start()`
    - 미디어를 녹화하기 시작한다.
  - `recorder.stop()`
    - 미디어 녹화를 중지한다.
    - `dataavailable` 이벤트를 발생시킨다.
  - `ondataavailable`
    - 녹화가 멈추면 실행되는 이벤트
    - `dataavailable`이벤트를 처리하여 사용가능한 `blob` 데이터를 응답해준다.
    - 미디어데이터를 포함하는 `blob`은 `dataavailable`이벤트의 `data` 속성에서 사용할 수 있다.
  - `createObjectURL()`
    - blob 객체를 url로 만든다.
```js

    const startBtn = document.getElemetById('startButton')
    const video = document.getElemetById('preview')

    let stream;
    let recorder;
    let videoFile;

    const handleDownload = () => {
        const a = document.createElement('a')
        a.href = videoFile;
        a.download = 'MyRecording.webm';
        document.body.appendChild(a);
        a.click();
    }

    const hnadleStop  =  () => {
        startBtn.innerText = 'Download Recording';
        startBtn.removeEventListner('startBtn', handleStop)
        startBtn.addEventListner('startBtn', hnadleDownload)
        recorder.stop();
    }


    const handleStart = () => {
        startBtn.innerText = 'Stop Recording';
        startBtn.removeEventListner('startBtn', handleStart)
        startBtn.addEventListner('startBtn', handleStop)
        recorder = new MediaRecorder(stream, { mimeType: 'video/webm' })
        recorder.ondataavailable = (e) => {
            videoFile = URL.createObjectURL(e.data)
            video.scrObject = null;
            video.src = videoFile;
            video.loop = true;
            video.play();
        }
        recorder.start();
    }

    // 실시간으로 사용자에게 보여지는 비디오 
    const init = async (e) => {
        stream = await navigator.mediaDevices.getUserMedia({ audio: false, video: true })
        video.srcObject = stream
        video.play();
    }

    init();

    startBtn.addEventListner('startBtn', handleStart)

```
> 프론트엔드에서 async/await를 사용하려면 `regenerator-runtime`을 설치해야 된다.</br>
> `npm install regenerator-runtime`</br>
> `main.js`에 import 하고 `main.js`를 모든 template에 추가 해준다.</br>
> ```js
> 
>   // main,js
> 
>   import regeneratorRuntime from 'regenerator-runtime';
> 
> ```
> `base.pug`
> ```
> 
>   script(src='/static/js/main.js')
> 
> ```

- `webpack.confog.js` 파일
```js

    mmodule.epxorts = {
        entry: {
            recorder: './scr/client/js/recorder.js',
        }
    }

```

- upload.pug
```pug

    video#preview
    div
        button#startButton Start Recording

    block script 
        script(src='/static/js/recorder.js')

```
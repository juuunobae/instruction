# Video Player Custom 
- 각 버튼 태그와 필요한 태그를 만들어주고 id를 달아준다.
- `src/client/js/videoPlayer.js` 파일을 만들어 준다.
## player custom 관련 JS 이벤트 및 메소드
- 태그들 불러오기
```js

    const video = document.querySelector('video') // #vidoe
    const playBtn = document.getElementById('play') // #button
    const muteBtn = document.getElementById('mute') // #button
    const currenTime = document.getElementById('currenTime') // #span
    const totalTime = document.getElementById('totalTime') // #span
    const timeline = document.getElementById('timeline') // #input.range
    const volumeRange = document.getElementById('volume') // #input.range
    const fullScreenBtn = document.getElementById('fullScreen') // #button
    const videoContainer = document.getElementById('videoContainer') // #video Container
    const videoControls = document.getElementById('videoControls')

```

### 비디오 재생 관련 이벤트, 메소드
- `.paused` 메소드
  - 현재 video가 일시정지 상태인지 알려주는 메소드
- `play(), pause()` 메소드
  - 비디오를 재생, 일시정시 시켜준다.
```js

    const handlePlayClick = (e) => {
        if(video.paused) {
            video.play();
        } else {
            video.pause();
        }
        playBtn.innerText = video.paused ? 'Play' : 'Pause';
    }

    playBtn.addEventListner('click', handlePlayClick);

```
- `'play', 'pause'` 이벤트
  - 비디오가 재생되거나 일시정지 되면 실행되는 이벤트
```js

    const handlePlay = (e) => playBtn.innerText = 'Play';

    const handlePause = (e) => playBtn.innerText = 'Pause';

    playBtn.addEventListner('play', handlePlay);
    playBtn.addEventListner('pause', handlePause);

```

### 비디오 소리 관련 이벤트, 메소드
- `.muted`
  - 현재 비디오가 음소거 상태인지 알려주는 메소드
- ``
```js

    const handleMute = () => {
        if(video.muted){
            video.muted = false;
        }else{
            video.muted = true;
        }
        muteBtn.innerText = video.muted ? 'Unmute' : 'Mute';
        volumeRange.value = video.muted ? 0 : volumeValue;
    }

    muteBtn.addEventListner('click', handleMute);

```
- `input` 이벤트
  - input의 값이 변경될 때 발생되는 이벤트
```js

    let volumeValue =  0.5
    vidoe.volume = volumeValue

    const handleVolumeChange = (e) => {
        const { target: { value } } = e
        video.volume = value;
        if(video.muted){
            video.muted = false;
            muteBtn.innerText  = 'Mute';
        }
        volumeValue = value;
        video.volume = value
    }

    volumeRange.addEventListner('input', handleVolumeChange)

```

### 비디오 재생 시간 관련 이벤트, 메소드
- `loadedmetadata` 이벤트
  - 비디오의 meta data가 로드될 때 실행
- `.duration` 메소드
  - 미디어의 전체 길이를 반환한다.
- `.timeUpdate` 메소드
  - 비디오 시간이 변경될 때 마다 실행
- `.currentTime` 메소드
  - 비디오가 현재 재생되고 있는 시간
```js

    const formatTime = (seconde) => new Date(seconds* 1000).toISOString().substr(11, 8);

    const handleLoadedMetadata = (e) => {
        totaltime.innerText = formatTime(Math.floor(video.duration));
        timeline.max = Math.floor(video.duration);
    }

    const handleTimeUpdate = (e) => {
        currentTime.innerText = formatTime(Math.floor(video.currentTime));
        timeline.value = Math.floor(video.currentTime);
    }

    const handleTimelineChange = (e) => {
        const { target: { value } } = e;
        video.currentTime = value;
    }

    video.addEventListner('loadedmetadata', handleLoadedMetadata)
    video.addEventListner('timeUpdate', handleTimeUpdate)
    timeline.addEventListner('input', handleTimelineChange)

```

### 비디오 화면 크기 관련 이벤트 및 메소드
- `.requestFullscreen` 메소드
  - 지정한 태그와 그 자손 태그들 까지 fullscreen 모드로 설정한다.
- `.exitFullscreen()`
  - 현재 fullscreen 모드로 표시되어 있는 모든 태그를 원래 상태로 되돌려 놓느다.
- `.fullscreenElement`
  - 현재 fullscreen 모드로 표시되는 element를 알려주고 없으면 null을 반환한다.
```js

    const handleFullscreen = () => {
        const fullscreen = document.fullscreenElement;
        if(fullscreen){
            document.exitFullscreen();
            fullScreenBtn.innerText = 'Enter Full Screen'
        }else {
            videoContainer.requestFullscrenn();
            fullScreenBtn.innerText = 'Exit Full Screen'
        }
    }

    fullScreenBtn.addEventListner('click', handleFullscreen)

```

### 비디오 조작 버튼 보이기, 숨기기
- `mousemove` 이벵트
  - 해당 태그에서 마우스의 움직임이 있으면 싷행된다.
- `mouseleave` 이벤트
  - 해당 태그에서 마우스가 떠나면 실행된다.
- 마우스의 멈춤을 인식하는 이벤트가 따로 없기 때문에 `setTimeout`과 `clearTimeout`을 이용해 직접 구현해야 한다.
  - controlsMovementTimeout은 마우스 움직임이 감지되면 생성되는 timeout의 리턴값을 담는 변수
  - timeout의 콜백함수 'hideControls'는 'showing' 클래스를 삭제해준다.
    1. 마우스 움직임이 인식되면 timeout이 생성되었는지 확인한다.
    2. timeout이 있다면 timeout을 없애주고 controlsMovementTimeout을 null로 바꿔준다.
    3. 그리고 timeout을 다시 생성해준다.
    4. 마우스 움직임이 멈추면 timeout의 콜백함수 hideControls는 실행될 것이고 'showing' 클래스를 삭제해 줄 것이다.
```js

    let controlsTimeout = null;
    let controlsMovementTimeout = null;

    const hideControls = () => {
        videoControls.classList.remove('showing')
    }

    const handleMouseMove = () => {
        if(controlsTimeout){
            clearTimeout(controlsTimeout)
            controlsTimeout = null
        }
        if(controlsMovementTimeout){
            clearTimeout(controlsMovementTimeout);
            controlsMovementTimeout = null
        }
        videoControls.classList.add('showing')
        controlsMovementTimeout = setTimeout(hideControls, 3000)
    }

    const handleMouseLeave = () => {
        controlsTimeout = setTimeout(hideControls, 3000);
    }

    video.addEventListner('mousemove', handleMouseMove)
    video.addEventListner('mouseleave', handleMouseLeave)

```
---
description: >-
  React 개발 환경에서 비디오 다운로드 버튼을 클릭 하였을경우, 다운로드 과정에서 강제적으로 Component를 닫았을 때 발생하는
  Error를 어떻게 처리하였는지의 과정을 설명합니다.
---

# Download UI

### Situation?

1. xhr\(XMLHttpRequest\)를 이용하여 다운로드 모션을 UI로 구현
2. 다운로드를 위하여 특정한 버튼을 클릭할 경우 Modal에서 다운로드 진행
3. 다운로드가 진행중인 과정에서 강제적으로 Modal을 닫을경우 에러를 발생

### Why?

다운로드 모션을 구현하기 위하여 xhr에 progress 이벤트를 등록하여 다운로드 진행 값을 받아오고 있는 상황에서 강제적으로 Component를 해제하면 Ummount 상태가 됩니다. Unmount된 곳에 progress 이벤트가 계속 발생되고 있기 때문에 아래와 같이 경고문을 받습니다.

> Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in a useEffect cleanup function.

위의 내용을 간단히 해석해 보면 Unmouted된 Component에 상태값을 업데이트 할 수 없다는 에러 메세지를 보여줍니다. 또한, 친절하게도 useEffect의 cleanup 함수를 이용하여 발생한 이벤트를 해제 하라고 가이드를 제공 합니다. 이러한 힌트를 바으로 어떻게 해결을 했는지 살펴 보겠습니다.

### How to?

React는 v16.8 이후로 Functional Component에서 Hook을 사용할 수 있으며 Lifecycle 또한 헨들링 할 수 있는 useEffect API를 제공합니다. useEffect cleanup 함수를 이용하여 위에서 이벤트를 구독하고 있던 내용을 해제 시켜주면 해당 이슈를 해결할 수 있습니다.

utils.js

```text
const _EVENT = {
  progress: null,
  xhr: null,
};

export const convertDownload = (payload) => {
  const { url, func, kill, progress } = payload;
  const xhr = new XMLHttpRequest();

  // Abort XHR
  if (kill) {
    xhr.removeEventListener('progress', _EVENT['progress']);
    _EVENT.xhr.abort();
    _EVENT.xhr = null;
    return false;
  }

  // Add Event
  _EVENT['progress'] = progress;

  xhr.responseType = 'blob';
  xhr.onload = () => {
    const blobUrl = URL.createObjectURL(xhr.response);
    func(blobUrl);
  };
  xhr.addEventListener('progress', _EVENT['progress']);
  xhr.open('GET', url);
  xhr.send();
  _EVENT.xhr = xhr;
};
```

ButtonDownload.jsx

```text
// Hook
const [url, setUrl] = useState('');
const [loading, setLoading] = useState(0);

// Func
const runProgress = useCallback(e => {
  let percent = 0;
  const position = e.loaded;
  const total = e.total;
  if (e.lengthComputable) {
    percent = Math.ceil((position / total) * 100);
    setLoading(percent);
  }
}, []);
const runDownload = useCallback(url => {
  const data = {
    url,
    progress: e => runProgress(e),
    func: blob => {
      setUrl(blob);
    },
  };
  convertDownload(data);
}, []);

useEffect(() => {
  href && runDownload(href);
}, [href]);

useEffect(() => {
  return () => { // cleanup
    const data = {
      kill: true,
    };
    convertDownload(data);
  };
}, []);
```

utils.js에서 파라미터 kill을 이용하여 event 실행, 해제 할 수 있는 플래그를 제공합니다.   
이를 해결하면서 저는 단순하게 removeEventListener 사용하여 이벤트를 해제시키면 행복하게 이슈가 해결 될 줄 알았습니다. 그러나 xhr 요청 중단하기 위해선 abort 메소드를 이용해야 합니다.

ButtonDownload.jsx에서 Unmount 시점에 cleanup 함수가 실행이 되며 파라미터로 이벤트를 해제할 수 있는 플래그\(kill: true\)값을 넘겨주어 abort 메소드를 실행하여 이슈를 해결 할 수 있었습니다.

abort를 사용하기 위해선 이미 요청된 xhr에 접근할 수 있어야 합니다. 이를 해결하기 위하여 EVENT 변수를 사용하여 다시 접근 할 수 있도록 구성하였습니다. __해당 로직은 단일 로직 이지만, 다중 로직이 된다면 \_EVENT변수에 값을 배열로 만들어 하나하나 접근해 abort 시킬 수 있습니다.

abort를 사용할 수 있던 좋은 기회였으며,   
xhr에 대해서 내용을 다룰때 다른 내용들을 포함하여 같이 다룰 수 있도록 하겠습니다.


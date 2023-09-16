# useDebugValue, useID, useSyncExternalStore

# 📌 useDebugValue

리액트 개발자 도구에서 **커스텀 훅**에 레이블을 추가해주는 훅

### 🌟 useDebugValue(value, format?)

- `value` : 리액트 개발자 도구에서 표시하려는 값.

- `선택적 format` : 포매팅 함수. 

### 🌟 사용법

```jsx
import { useDebugValue } from 'react';

function useOnlineStatus() {
  // ...
  useDebugValue(isOnline ? 'Online' : 'Offline');
  // ...
}
```

- 커스텀 훅의 최상위 레벨에서 호출하여 개발자 도구가 읽을 수 있는 디버그 값 표시

![image](https://github.com/saseungg/TIL/assets/115215178/48edd4d3-a00d-4674-a2dc-3d7d1e54d474)

- 이렇게 하면 useOnlineStatus를 호출하는 컴포넌트에 OnlineState와 같은 레이블이 지정이 되는 걸 볼 수 있음
- 검사하기 어려운 복잡한 내부 데이터를 가진 커스텀 훅에 유용.

### 🌟 포매팅 함수

```jsx
useDebugValue(date, date => date.toDateString());
```

- 포매팅 함수는 디버그값을 매개변수로 받고 변환된 값을 반환한다.
- 컴포넌트가 검사할때, 인수로 포매팅 함수를 호출하고 반환된 포매팅값을 표시한다.
    - 지정하지 않으면 원본  value 표시

### 🌟 사용 목적

- 이렇게 하면 컴포넌트를 실제로 조사하지 않는 한 비용이 많이 들 수 있는 포매팅 로직을 실행하지 않아도됨.
- 예를 들어, `date`가 날짜 값인 경우, 컴포넌트를 렌더링 할 때마다 `toDateString()`을 호출하지 않는다.


# 📌 useId

고유 ID 생성해주는 훅 (접근성 속성에 전달할수 있는 ID를 생성할 때 유용)

### 🌟 사용법

- useId 목록에서 키를 생성하기 위해 사용하지 말아야함.
    - (컴포넌트 최상단에서 호출해야해서 어차피 사용 못할듯 → map을 돌면서 키를 생성하지 못하고 사용하려면 각각의 변수에 다 담아서 넣어줘야하기 때문)

### 🌟 사용 예시

**1. 두 태그가 연관되어 있음을 알리기 위해**

```jsx
import { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  return (
    <>
      <label>
        Password:
        <input
          type="password"
          aria-describedby={passwordHintId}
        />
      </label>
      <p id={passwordHintId}>
        The password should contain at least 18 characters
      </p>
    </>
  );
}
```

- 생성한 id를 연관된 태그 id값으로 전달해줄 수 있음
- 접근성에는 id값이 없으면 연관된 태그인지 인식을 못하는데 해당 id값을 일치 시킴으로써 서로 연관이 있음을 연관지을 수 있어서 유용할듯.

**2. 접두사로 사용**

```jsx
const id = useId();

<label htmlFor={id + '-firstName'}>First Name:</label>
<input id={id + '-firstName'} type="text" />

<label htmlFor={id + '-lastName'}>Last Name:</label>
<input id={id + '-lastName'} type="text" />
```

- 라벨과 input은 같은 htmlFor와 id값이 일치해야 매칭이 되는데 해당 id값을 사용후 접두사를 추가하여 일치시키는 방법도 있다.

# 📌 useSyncExternalStore

외부 스토어를 구독할 수 있는 훅

### 🌟 useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)

- `subscribe` : 하나의 callback 인수를 받아 스토어를 구독하는 함수. 스토어가 변경되면 제공된 callback을 호출하고 컴포넌트가 리렌더링한다.
- `getSnapshot` : 컴포넌트에 필요한 스토어 데이터의 스냅샷을 반환하는 함수
- `선택적 getServerSnapshot` : 스토어에 있는 데이터의 초기 스냅샷을 반환하는 함수

### 🌟 브라우저 API 구독

- 리액트에서 오프라인 상태를 제대로 감지하지 못하는 경우가 있음 그럴 경우 useSyncExternalStore를 사용한다.

```jsx
import { useSyncExternalStore } from 'react';

export default function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function getSnapshot() {
  return navigator.onLine; ..
}

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}
```

[Window: online event - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Window/online_event)

### 🌟 예시) useState일 경우

![화면 기록 2023-09-05 오전 11 37 31](https://github.com/saseungg/TIL/assets/115215178/9ec2d24e-11c3-4f37-b91b-3233f1d24def)

- 온라인 상태면 처음부터 ✅ Online 표시가 되어야하는데 네트워크를 껐다 키니까 제대로 인식이 됨. ⇒ 제대로 감지를 못한다.

[code 자세히 보기](https://codesandbox.io/s/serverless-wave-x8626g?file=/App.js)

### 🌟 예시) useSyncExternalStore인 경우

![화면 기록 2023-09-05 오전 11 43 40](https://github.com/saseungg/TIL/assets/115215178/bfc69afa-ab58-41a0-88b5-c1151e77233d)


- 실시간으로 네트워크 감지가 된다

[code 자세히 보기](https://codesandbox.io/s/xyfwfj?file=%2FuseOnlineStatus.js&utm_medium=sandpack)
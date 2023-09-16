# memo, startTransition

# 📌 memo

props가 이전값과 동일하지 않을 경우에만 리렌더링 한다.

```jsx
const MemoizedComponent = memo(Componene, arePropsEqual?)
```

- `Componenet` : 메모화 하려는 컴포넌트. memo는 이 컴포넌트를 수정하지 않고 새롭게 메모화된 컴포넌트를 반환한다.
- 선택적 `arePropsEqual` : 컴포넌트의 이전 prop 및 새로운 prop의 두 인자를 받는 함수. 새로운 prop이 이전 prop과 같으변 true 반환! 다르면 false!
    - Object.is로 각 prop을 비교한다.
- memo를 적용하더라도 컴포넌트의 state나 사용중인 context가 변경되면 리렌더링
- 특정 인터렉션이 느리게 느껴진다면 React 개발자 도구 profiler를 사용해 어떤 컴포넌트가 메모화를 통해 가장 큰 이점을 얻을 수 있는지 확인하고 필요한 경우 메모화해라.

### 🌟 예제)

```jsx
import { memo, useState } from 'react';

export default function MyApp() {
  const [name, setName] = useState('');
  const [address, setAddress] = useState('');
  return (
    <>
      <label>
        Name{': '}
        <input value={name} onChange={e => setName(e.target.value)} />
      </label>
      <label>
        Address{': '}
        <input value={address} onChange={e => setAddress(e.target.value)} />
      </label>
      <Greeting name={name} />
    </>
  );
}

const Greeting = memo(function Greeting({ name }) {
  console.log("Greeting was rendered at", new Date().toLocaleTimeString());
  return <h3>Hello{name && ', '}{name}!</h3>;
});
```

- 보통 상위 컴포넌트가 렌더링되면 자식 컴포넌트도 렌더링됨. memo로 감싸주면 상위 컴포넌트가 렌더링이 되도 자식이 props가 변경되지 않는 이상 유지된다. (state, context 제외)

### 🌟 props 변경 최소화하기

1. **useMemo를 사용하여 부모컴포넌트가 해당 객체를 다시 만드는 것을 방지**

```jsx
function Page() {
  const [name, setName] = useState('Taylor');
  const [age, setAge] = useState(42);

  const person = useMemo(
    () => ({ name, age }),
    [name, age]
  );

  return <Profile person={person} />;
}

const Profile = memo(function Profile({ person }) {
  // ...
});
```

1. **전체 객체 대신 개별값으로 사용**

```jsx
function Page() {
  const [name, setName] = useState('Taylor');
  const [age, setAge] = useState(42);
  return <Profile name={name} age={age} />;
}

const Profile = memo(function Profile({ name, age }) {
  // ...
});
```

props가 해당 요소에 필요한 최소한의 정보만을 받고 있는지 확인한다.

```jsx
function GroupsLanding({ person }) {
  const hasGroups = person.groups !== null;
  return <CallToAction hasGroups={hasGroups} />;
}

const CallToAction = memo(function CallToAction({ hasGroups }) {
  *// ...
});*

```

불리언 값을 넣어서도 활용 가능하다.

### 🌟 사용자 정의 비교 함수 지정

메모화된 컴포넌트의 props 변경을 최소화하는 것이 불가능할 수 있음. 

이 경우 React가 이전 props와 새 props를 비교할 때 얕은 동등성 대신 사용하도록 커스텀 비교 함수 사용. 

```jsx
const Chart = memo(function Chart({ dataPoints }) {
  // ...
}, arePropsEqual);

function arePropsEqual(oldProps, newProps) {
  return (
    oldProps.dataPoints.length === newProps.dataPoints.length &&
    oldProps.dataPoints.every((oldPoint, index) => {
      const newPoint = newProps.dataPoints[index];
      return oldPoint.x === newPoint.x && oldPoint.y === newPoint.y;
    })
  );
}
```

- 비효율적이라 실제로 잘 사용하지는 않을 것 같음

# 📌 startTransition

UI를 차단하지 않고 state를 업데이트 할 수 있다.

![image](https://github.com/saseungg/TIL/assets/115215178/d7648202-82f9-4841-9569-b27d5c4aa962)
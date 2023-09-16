# createContext, forwardRef, lazy
# 📌 createContext

useContext를 사용할 때 provider가 없을 경우 가져다 쓰는 기본 컨텍스트 값

```jsx
const ThemeContext = createContext(defaultValue)
```

defaultValue는 상위 트리에 일치하는 **provider가 없는 경우**에 적용된다. 이 값은 절대 변하지 않는다.

`useContext` 이전에는 `SomeContext.Consumer` 라는 것을 사용해서 컨텍스트를 읽었음

### 🌟 Consumer

```jsx

function MyComponent() {
  return (
    <MyContext.Consumer>
      {contextValue => (
        <div>
          <p>{contextValue.someData}</p>
          <button onClick={contextValue.someFunction}>Click Me</button>
        </div>
      )}
    </MyContext.Consumer>
  );
}
```

context 값을 인자로 받아서 넣어줘야한다.

### 🌟 useContext

```jsx
function MyComponent() {
  const contextValue = useContext(MyContext);

  return (
    <div>
      <p>{contextValue.someData}</p>
      <button onClick={contextValue.someFunction}>Click Me</button>
    </div>
  );

```

useContext는 변수에 context값을 담아서 사용할 수 있음. 훨씬 간편해짐

## 🌟 컨텍스트 import, export

다른 파일에 있는 컴포넌트가 동일한 컨텍스트에 엑세스해야할 경우가 종종 있기 때문에 컨텍스트를 별도의 파일에 선언하는 것이 일반적이다.

```jsx
// Contexts.js
import { createContext } from 'react';

export const ThemeContext = createContext('light');
export const AuthContext = createContext(null);
```

---

# 📌 forwardRef

부모 컴포넌트가 자식 컴포넌트에 ref를 전달하고 싶을 때 사용

- focus에 주로 사용

```jsx
const SomeComponent = forwardRef(render)
```

forwardRef는 렌더링 함수를 인자로 받는다. React는 props 및 ref화 함께 이 함수를 호출한다.

```jsx
import React, { forwardRef } from 'react';

const MyInput = forwardRef(props, ref) => {
	return <input ref={ref} />;
};

export default MyInput;
```

아래 같은 방법으로도 작성 가능하다.

```jsx
import React, { forwardRef } from 'react';

const MyInput = (props, ref) => {
	return <input ref={ref} />;
};

export default forwardRef(MyInput);
```

# 📌 lazy

해당 메서드를 사용하면 동적 가져오기를 사용해서 구성 요소 수준에서 React  애플리케이션을 쉽게 코드 분할 할 수 있다.

### 🌟 Suspense와 활용

```jsx
import React, { lazy, Suspense } from 'react';

const AvatarComponent = lazy(() => import('./AvatarComponent'));

const renderLoader = () => <p>Loading</p>;

const DetailsComponent = () => (
  <Suspense fallback={renderLoader()}>
    <AvatarComponent />
  </Suspense>
```

### Q. Suspense 안에 그냥 컴포넌트를 넣어도 로딩이 끝나면 이후에 보여주는데 lazy를 쓰는이유가 뭘까?

가장 큰차이는 네트워크에 있다고 보면됨. suspense 안에 컴포넌트를 넣으면 렌더링이 되지는 않지만 해당 컴포넌트에 있는 이미지나 데이터들이 네트워크에 전송은 될 것이다. lazy를 쓰면 해당 컴포넌트의 코드는 앱이 처음 로딩될 때 전송되지 않고, 실제로 필요한 시점에 네트워크 요청을 통해서 가져온다.
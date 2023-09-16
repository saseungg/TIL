# \<Fragment>, \<Profiler>, \<StrictMode>, \<Suspense>

# 📌  `<Fragment>`

- jsx의 루트 태그를 노드 없이 반환하고 싶을 때 사용할 수 있다.
    - <></> 로 감사면 노드가 생기지 않음.
- <Fragment> 구문으로 선언한 Fragment에는 키를 추가할 수 있다.

# 📌  `<Profiler>`

React 트리의 렌더링 성능 측정

### 🌟 onRender 콜백 매개변수
- `id`: 방금 커밋한 <Profiler> 트리의 id 문자열 prop. 
  - 여러 프로파일러를 사용하는 경우 트리의 어느 부분이 커밋되었는지 식별 가능

- `phase`: "mount","update" 혹은 "nested-update". 
  - 트리가 처음 마운트되었거나, props, state 또는 훅의 변경으로 인해 다시 렌더링되었는지 파악 가능.


- `actualDuration`: 현재 업데이트에 대해 <Profiler> 및 하위 컴포넌트들을 렌더링하는 데 걸린 시간(밀리초). 
  - 이 값은 하위 트리가 메모화(예: memo, useMemo)를 얼마나 잘 사용하는지를 나타낸다.


- `baseDuration`: 최적화 없이 전체  <Profiler> 하위 트리를 다시 렌더링하는 데 걸리는 시간을 추정한 값(밀리초)입니다. 
  - 트리에 있는 각 컴포넌트의 가장 최근 렌더링 시간을 합산하여 계산한다.
  - 이 값은 최악의 렌더링 비용(예: 초기 마운트 또는 메모화가 없는 트리)을 추정. 

- `startTime`: React가 현재 업데이트 렌더링을 시작한 시점에 대한 숫자 타임스탬프.

- `endTime`: React가 현재 업데이트를 커밋한 시점의 타임스탬프.

### 🌟 사용 예시

![화면 기록 2023-09-08 오전 12 05 01](https://github.com/saseungg/TIL/assets/115215178/cf357f0d-d8c0-4bfb-af9b-818a32680163)

### 🌟 참고하면 좋을 자료
[[번역] ReactJS의 성능 최적화를 위한 5가지 추천 툴 ](https://github.com/sbyeol3/articles/issues/14)

[[번역] 리액트 프로파일러를 활용한 리액트 앱 성능 향상](https://velog.io/@cookie004/%EB%B2%88%EC%97%AD%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%94%84%EB%A1%9C%ED%8C%8C%EC%9D%BC%EB%9F%AC%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EC%95%B1-%EC%84%B1%EB%8A%A5-%ED%96%A5%EC%83%81)

# 📌  `<StrictMode>`

- 불완전한 렌더링으로 인한 버그를 찾기 위해 한번 더 렌더링한다.
- Effect 클린업이 누락되어 발생한 버그를 찾기위해 Effect를 한번 더 실행한다.
    - 셋업 + 클린업 사이클을 한번 더 실행
    - 두번 실행함으로써 클린업이 제대로 동작하는지 확인이 가능함.
        - ex) 1씩 증가하는 코드가 있다했을 때 클린업으로 카운트를 초기화 시켜주는 코드를 넣음. 근데 만약 초기화가 안되고 숫자가 계속 카운트된다면? 클린업이 제대로 동작을 안하는거임!
- 지원 중단된 API의 사용 여부를 확인한다.
    - StrictMode로 감싼 트리 내의 컴포넌트가 더이상 지원되지 않은 API를 사용할 경우 경고를 표시한다.
- 모든 검사는 개발 환경 전용이며 상용 빌드에서는 영향을 미치지 않는다.

# 📌 `<Suspense>`

- 자식요소가 로딩이 걸릴때까지 다른 컴포넌트를 보여주는 것
    - 스피너나 스켈레톤 같은 플레이스 홀더 뷰일때 사용하면 좋음

```jsx
<Suspense fallback={<Loading />}>
	<SomeComponent />
</Suspense>
```

- `children` : 렌더링 하려는 실제 UI. 렌더링 하는 동안 children이 일시 중단되면 Suspense 경계가 fallback 렌더링으로 전환된다.
- `fallback`: 로딩이 완료되지 않은 경우에 실제 UI 대신 렌더링할 대체 UI.
- Suspense는 childre이 일시 중단되면 자동으로 fallback으로 전환되고, 데이터가 준비되면 다시 children으로 전환된다. 렌더링 중에 fallback이 일시 중단되면 가장 가까운 상위 Suspense 경계가 활성화된다.

### 🌟 콘텐츠 한번에 드러내기

```jsx
<Suspense fallback={<Loading />}>
  <Biography />
  <Panel>
    <Albums />
  </Panel>
</Suspense>
```

Suspense 내부의 전체 트리는 단일 단위 취급이 되기 때문에 내부 컴포넌트들이 전부 완료할때까지 폴백을 표시한다.

### 🌟 Suspense 중첩

```jsx
<Suspense fallback={<BigSpinner />}>
  <Biography /> 
  <Suspense fallback={<AlbumsGlimmer />}>
    <Panel> 
      <Albums />
    </Panel>
  </Suspense>
</Suspense>
```

중첩함으로써 albums이 로드될때까지 기다리지 않고 bigspinner를 표시했다가 biography가 완료되면 먼저 보여준다.

### 🌟 Suspense를 활용한 에러바운더리
![화면 기록 2023-09-08 오후 4 42 21](https://github.com/saseungg/TIL/assets/115215178/32e7fc03-d619-4bcc-ae4c-792f5fd08a0f)

### 🌟 화면 설명
1. 서버 에러 발생
2. Suspense 컴포넌트에 있는 스피너 동작
3. 클라이언트에서 동일한 컴포넌트 다시 렌더링 시도 -> 에러!
4. 가까운 에러 바운더리(에러경계)를 표시한다.

### 🌟 보충 설명
스트리밍 서버 렌더링 API 중 하나 (또는 이에 의존하는 프레임워크)를 사용하는 경우, React는 서버에서 발생하는 오류를 처리하기 위해 <Suspense> 바운더리도 사용한다. 컴포넌트가 서버에서 에러를 발생시키더라도 React는 서버 렌더링을 중단하지 않음. 대신, 그 위에 있는 가장 가까운 <Suspense> 컴포넌트를 찾아서 생성된 서버 HTML에 그 폴백(예: 스피너)을 포함시킨다. 사용자는 처음에 스피너를 보게됨.


클라이언트에서 React는 동일한 컴포넌트를 다시 렌더링하려고 시도. 클라이언트에서도 에러가 발생하면 React는 에러를 던지고 가장 가까운 에러 경계를 표시한다. 그러나 클라이언트에서 에러가 발생하지 않는다면, 결국 콘텐츠가 성공적으로 표시되었기 때문에 React는 사용자에게 에러를 표시하지 않는다.


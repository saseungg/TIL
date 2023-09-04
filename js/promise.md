# 프로미스
## Promise란?

- 자바스크립트 객체에서 비동기를 간편하게 처리할 수 있게 도와주는 객체.
- 이전에는 비동기 처리로 콜백 패턴을 주로 사용했으나 콜백 지옥으로 인해 가독성이 좋지 않았고, 비동기 처리 중에 발생한 에러 처리가 까다로워 이러한 단점을 보완하기 위해 등장했다.

## 콜백과 프로미스

```jsx
/* callback */
function add10(a, callback){
   setTimeout(() => callback(a + 10), 100);
}

add10(5, res => {
   console.log(res); // 15
});
```

```jsx
/* promise */
function add20(a){
   return new Promise(resolve => setTimeout(() => resolve(a + 20), 100));
}

add20(5)
   .then(console.log); // 25
```

- Promise는 Callback과 달라 결과를 값으로 받아서 저장할 수 있다.
- Promise는 반환만 하면 되어, 따로 콜백을 받을 필요가 없다.
- 결과 그 자체를 값으로 받기 때문에, 연속으로 실행하는 코드에선 then()만 이용하면되므로 Callback 보다 더 가독성 있는 코드가 된다.

### 추가 예시

```jsx
/* callback */
add10(5, res => {
   add10(res, res => {
      add10(res, res => {
         console.log(res); //35
      }); // Callback Hell...
   });
});
```

```jsx
/* promise */
add20(5)
   .then(add20)
   .then(add20)
   .then(console.log); //65
```

## 프로미스 생성

```jsx
// Promise 객체의 생성
const promise = new Promise((resolve, reject) => {
  // 비동기 작업을 수행

  if (/* 비동기 작업 수행 성공 */) {
    resolve('Success');
  }
  else { /* 비동기 작업 수행 실패 */
    reject('Failed');
  }
});
```

Promise로 구현된 비동기 함수는 Promise 객체를 반환하며, 이로 구현된 비동기 함수를 호출하는 측에서 Promise 객체의 후속 처리 메서드(then, catch)를 통해 비동기 처리 결과 또는 에러메세지를 전달받아 처리한다. 

- then : 두 개의 콜백 함수를 인자로 받으며, 첫번째 함수는 성공 시, 두번째 함수는 실패 시 호출된다. then은 Promise를 반환한다.
- catch : 에러가 발생하면 호출되며, then의 두번째 인자와 같은 역할을 한다. catch는 Promise를 반환한다.
- 첫번째 콜백 함수 내부에서 오류가 날경우, 오류를 제대로 잡지 못하는 경우도 있으므로, 가급적 에러처리는 catch()통해 하는게 좋다.

## 프로미스의 상태

- Pending(대기) : 비동기 처리 로직이 아직 미완료인 상태
- Fulfilled(이행) : 비동기 처리가 완료되어 Promise가 결과값을 반환해준 상태
- Refected(실패) : 비동기 처리가 실패하거나 오류가 발생한 상태

### Pending

Promise를 호출하면 Pending 상태가 된다. 이때 콜백 함수의 인자로 resolve, reject에 접근할 수 있다.

```jsx
new Promise(function(resolve, reject){
    // ...
});
```

### Fufilled

콜백 함수의 인자 resolve를 실행하면 Fulfilled 상태가 된다. (완료 상태)

```jsx
new Promise(function (resolve, reject) {
   resolve();
});
```

### Rejected

콜백 함수의 인자 reject를 실행하면 Rejected 상태가 된다. (실패)

이후 실패 상태가 되면 catch()를 통해 error를 다룰 수 있다.

```jsx
new Promise(function(resolve, reject) {
  reject();
});
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a7b932a1-ce2f-44e2-9eaa-3e213c591f12/06793e09-f47a-406e-a4f0-756c8735ad78/Untitled.png)

Promise는 정해진 기능을 수행했을때, 정상적으로 이루어지면 성공, 예상치 못한 문제가 발생하면 에러를 전달해준다. 또 비동기 작업을 하는 함수의 리턴타입으로 쓰이며, 어떤 함수가 비동기적인 작업을 한다고 하면, 그 함수는 프로미스를 반환한다.

## 프로미스 사용법

```jsx
const devide = (num, num2) => {
	return new Promise((resolve, reject) => {
		if (num2 !==0) {
			resolve(num1 / num2);
		} else {
			reject(new Error("0으로 나눌 수 없습니다."));
		}
	})
}
```

```jsx
/* Success: 두번째 인자가 0이 아닐 경우*/
devide(10, 5)
   .then((value) => console.log("성공:", value))
   .catch((error) => console.log("실패:", error))
// 성공: 2

/* Failed: 두번째 인자가 0일 경우*/
devide(10, 0)
   .then((value) => console.log("성공:", value))
   .catch((error) => console.log("실패:", error))
// 실패: Error: 0으로 나눌 수 없습니다.
//  at <anonymous>:6:17
//  at new Promise (<anonymous>)
//  at devide (<anonymous>:2:11)
//  at <anonymous>:16:1
```

정상적인 인자를 넘긴 경우엔 then()이 호출되고, 비정상적인 인자를 넘긴 경우엔 catch가 호출됨을 알 수 있다.

하지만 주로 코딩을 할 때는 위와 같이 프로미스를 직접 생성해서 리턴해주는 코드보다는 어떤 API를 호출해서 리턴 받은 프로미스 객체를 사용하는 경우가 더 많다.

`fetch API`가 있다. 이는 브라우저 내장함수로 네트워크 요청을 날리기 위한 API이다. 요청을 보내고 응답을 받는 과정에는 불가피하게 딜레이가 발생할 수밖에 없다.

fetch 함수 역시 Promise 객체를 반환한다.

```jsx
fetch("요청할 URL")
   .then((res) => console.log(res)) //정상 응답 시 resolve() 호출
   .catch(err) => console.log(err)) //비정상 응답 시 reject() 호출
```

## 참고

[비동기 처리를 위한 Promise 객체](https://spicycookie.me/JavaScript/promise/)
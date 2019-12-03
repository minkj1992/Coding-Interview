# JavaScript

## JS의 싱글 스레드와 비동기 처리
> 싱글 스레드로 동작한다는 JS가 어떻게 비동기를 하는가

Node.Js의 실행환경은 `Js 엔진(Call Stack + Memory Heap)` + `Web Api` + `Event Loop` + `Message Queue`로 구성된다.

- `JS 엔진`
    - 자바스크립트 엔진 = `Memory Heap` + `Call Stack` x 1
    - `onDemand`(요구가 있으면 언제든지) 방식으로 작업 처리
    - 오래 걸리는 함수를 백그라운드(Web API)로 보내고 다음 task를 진행하며, `CallBack`함수를 통해서 보낸 Api에대한 작업을 Stack 다시 받아서 처리한다. 
    - `NonBlocking`방식
        - I/O작업이 완료될 때까지 wait하지 않고 알아서 작업한다.

- `WEB API(백그라운드)`
    - 브라우저에서 제공되는 API
    - JS엔진이 보낸 비동기 작업들을(DOM, AJAX, setTimeout등) 실행 후 결과값을 callback형식으로 TaskQueue에 넣어준다.

- `Task Queue (=Callback Queue)`와 `Event Loop`
    - Web API에서 넘겨받은 Callback 함수를 Enqueue 시킨다.
    - `Event Loop`를 통해서 `Call Stack.isempty() && !TaskQueue().isempty()`이면 `dequeue`를 시켜 Call Stack에 Callback함수를 Stack 시켜준다.

```js
setTimeout(function() {
    console.log("Bye, World!");
}, 0);
console.log("Hello, World!")
```

- 16:15
## 동기와 비동기 / 콜백 / promise / Async & await

- 동기: I/O-bound task의 경우 wait이 필현적으로 필요하며, 이에 따라 프로그램이 응답하지 못한다.

- 비동기(asynchronous)가 등장함.

- 콜백func (call / back)
    - 작업의 결과물을 콜백함수를 통해 사용 가능
    - event -> 콜백

- 콜백헬
    - 콜백함수가 늘어날 수록 코드의 깊이가 늘어남
    - 중첩 intent nesting되면서 가독성이 떨어짐

- Promise
    - 1. 콜백헬 구원
    - 2. 콜백패턴이 처리 순서를 보장하지 않는 문제 해결(.then 형식을 통해 비동기 처리에 대한 순서 제공 가능)
    - 3. 비동기처리 함수는 콜백함수를 return 하고 콜스택을 빠져나가게 되어 에러 발생 시, catch가 어렵다. (promise는 에러는 .catch())

    - 콜백을 예측 가능한 패턴으로 사용하게 도와줌 (`resolve` / `reject`)
    - 콜백함수 안, 프로미스 객체를 활용해 콜백에 대한 성공/실패/오류에 따라 후속 처리를 할 수 있다. (`then()`/`catch()`)
    - 비동기함수의 실행순서를 제어 가능
    - 가독성

- Async Await (파이썬에서 await은 coroutine과 비슷)
    - await라는 키워드를 통해 실행 지연 기능 추가
        - await 뒤에는 Promise객체를 반환하는 비동기 함수여야 함.
    - async 함수의 리턴 값은 resolve 된 Promise 객체, 혹은 reject 된 Promise 객체를 암묵적으로 리턴합니다.

[non blocking await의 개념](https://stackoverflow.com/questions/46004290/will-async-await-block-a-thread-node-js)

## Hoisting
- `hoist`: 끌어올리기
    - `var` keyword, `function`로 선언된 모든 변수 선언은 호이스트 된다.
    - `hoist`란 변수의 정의가 그 범위에 따라 `선언`, `할당`으로 분리 되는 것
    - 함수 선언 또한 hoist되어 함수 선언 이전에 call해도 함수 선언을 불러올 수 있다.(global 객체로 등록)
선언문은 항시 자바스크립트 엔진 구동시 가장 최우선으로 해석하므로 호이스팅 되고, 할당 구문은 런타임 과정에서 이루어지기 때문에 호이스팅 되지 않는다.

## `Closure`
- 두 개의 함수로 만들어진 환경
- 이를 통해 자바스크립트에 제공하지않는 `private`/`public` 속성을 구현할 방안을 마련


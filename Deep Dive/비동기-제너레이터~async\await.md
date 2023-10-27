# 제너레이터, async/await

## 제너레이터

ES6에 도입되었으며, 코드블록의 실행을 중지헀다가 필요한 시점에 재개할 수 있는 함수.

- 제너레이터 함수는 함수를 호출한 측에서 함수 실행을 중지시키거나 재개할 수 있다.
- 일반 함수는 매개변수를 통해 값을 함수에게 전달하고, return 을 통해 함수에서 호출자로 값을 전달한다
  하지만 제너레이터는 함수 호출자와 함수 양방향으로 상태를 주고받을 수 있다.
- 제너레이터 함수는 제너레이터 객체를 반환한다.
  > 제너레이터 객체는 이터러블이면서 이터레이터인 객체이다.

## 제너레이터 함수의 정의

```javascript
function* func1() {
  yield 1;
}
```

제너레이터 함수는 function\* 키워드로 선언한다.
제너레이터 함수는 하나 이상의 yield 표현식을 포함해야 한다.

> \*를 애스터리스크 라고 읽는다.

## 제너레이터의 일시정지와 재개

제너레이터는 yield 키워드와 next 메서드를 통해서 제너레이터 속 코드를 일시중지와 재개 할 수 있다.

1. 제너레이터 함수를 실행하면 제너레이터 객체가 반환된다.
2. 함수 호출자는 반환된 제너레이터 객체를 이용해서 next메서드를 호출하여 제너레이터 함수의 코드 블록을 실행시킨다.
3. 제너레이터 함수의 코드블록은 yield 표현식을 만나기 전까지 실행되고, yield 표현식을 만나게 되면 함수의 실행을 일시 정지하고, yield 뒤에 오는 표현식의 결과를 반환한다.
4. 다시 next 메서드를 호출하게되면 정지되었던 부분부터 다시 3번을 반복한다.

```js
function* func1() {
  yield 1;
  yield 2;
  yield 3;
}

const generator = func1();
// generator에 제너레이터 객체가 할당된다.

let a = generator.next();
// {value : 1, done : false}
let b = generator.next();
// {value : 2, done : false}
let c = generator.next();
// {value : 1, done : false}
generator.next();
// {value : undefined , done : true}
```

### next 메서드의 인수

이터레이터의 next 메서드와 다르게 제너레이터는 인수를 전달할 수 있다.
제너레이터 함수 내에서 yield 표현식을 할당받는 변수에 이 인수가 전달되게 된다.

```js
function* func1() {
  // 주의할 점은 x, y 변수에 할당되는 값은 yield 표현식이 평가된 값이 아니라 yield 평가 후 다음 next 호출 시 전달되는 인수가 저장된다.
  const x = yield 1;

  const y = yield x + 10;

  return x + y;
}
const generator = func1();

console.log(generator.next());
// {value : 1, done : false}

console.log(generator.next(10));
// {value : 20, done : false}

console.log(generator.next(20));
// {value : 30, done : true}
```

첫번째 next 메서드가 호출되면, 가장 처음에 만나는 yield 표현식 까지만 코드를 실행한다.

```js
const x = yield 1;

// --- 첫번째 next 메서드
// yield 1 이 평가되어 next메서드의 반환값으로 {value :1, done : false}가 반환된다.

// --- 두번쨰 next 메서드 실행 시
// const x 에는 두번째 next 메서드의 인수가 할당되고,
// 두번째 yield 표현식인 yield (x + 20)이 평가되어 반환된다.
```

여기서 주의할 점은 yield 표현식이 변수 x에 할당되는 것 까지 실행되는 것이 아니라,
표현식 평가 까지만 실행된다는 것이다.

변수 x에는 다음 next 메서드의 인수가 전달된다.

## 제너레이터의 활용

제너레이터를 사용하여 간단히 이터러블을 구현할 수 있다.

### 비동기 처리

제너레이터 함수를 이용하여 비동기 처리를 동기 처리처럼 구현할 수 있다.

```javascript
const async = (generatorFunc) => {
  const getnerator = generatoerFunc();

  const onResolved = (arg) => {
    const result = generator.next(arg);

    return result.done
      ? result.value
      : result.value.then((res) => onResolved(res));
  };

  return onResolved;
};

async(function* fetchTodo() {
  const url = "https://jsonplaceholder.typicode.com/todos/1";

  const response = yield fetch(url);
  const todo = yield response.json();
  console.log(todo);
})();
```

이렇게 제너레이터 함수를 이용하여 비동기 처리를 동기 처리처럼 구현하게 되면, 비동기 함수의 결과를 콜백 함수 형태로 전달하지 않고도 처리할 수 있는 장점이 있다.

하지만 코드가 장황하고 가독성이 좋지 못하다. 그래서..

## async / await

ES8에 도입된 async/await키워드를 이용하면 제너레이터보다 가독성 좋게 비동기 처리를 동기 처리처럼 구현할 수 있다.

async / await는 프로미스의 후속 메서드에 콜백 함수를 전달할 필요 없이 동기 처리처럼 프로미스를 사용할 수 있다.

```js
async function fetchTodo() {
  const url = "https://jsonplaceholder.typicode.com/todos/1";

  const response = await fetch(url);
  const todo = await response.json();
}
```

### async

함수 키워드 function 앞에 async 키워드를 사용하여 `asycn함수`를 정의할 수 있다.

async 함수는 언제나 프로미스를 반환하는 함수이다.

- 클래스의 constructor 메서드에는 async을 사용할 수 없다. constructor 메서드는 인스턴스를 반환해야 하기 때문이다.

> 명시적 반환을 하지 않아도 암묵적으로 프로미스를 반환한다.

### await 키워드

await 키워드는 비동기 처리가 settled 상태가 될 떄 까지 대기하다가 settled 상태가 되면 프로미스가 resolve를 한 처리 결과를 반환한다.
await 키워드는 언제나 프로미스 앞에서 사용해야 한다.

```js
async function getGitgubUserName(id) {
  const res = await fetch(`https://api.github.com/users/${id}`);
  // fetch 함수가 수행하는 비동기 처리가 완료될 때 까지 대기하다가 완료되면 결과를 res 변수에 할당한다.

  const { name } = await res.json();
  // json메서드도 프로미스를 반환한다. json 메서드의 비동기 처리가 완료되면 결과를 name 변수에 객체 구조분해 할당한다.
}
```

## 에러 처리

콜백 패턴의 단점은 에러 처리가 곤란한 것이다.
콜백 패턴을 try/catch 문으로 감싸도 에러를 캐치할 수 없다.

하지만 async/await를 이용한 비동기 처리의 경우 try/catch 문을 적용할 수 있다.

- async 함수 내부에서 try/catch문 사용하는 방법
- async 함수도 프로미스를 반환하므로 후속 처리메서드 catch를 이용하는 방법

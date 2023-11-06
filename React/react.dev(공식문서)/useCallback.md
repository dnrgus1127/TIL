
# useCallback

`useCallBack` Hook은 컴포넌트의 리렌더링 사이에 **함수 정의**를 캐시할 수 있는 리액트 훅이다.

### 목차

- [useCallback](#usecallback)
- [응용](#응용)
  - [불필요한 리 렌더링 막기](#불필요한-리렌더링-막기)

## useCallback(fm, dependencies)

두개의 매개변수를 가진다.

#### fn

캐시할 함수를 전달받는 매개변수.<br>
React는 초기 렌더링중 함수를 반환하고 캐시해서 저장한다, 다음 re-렌더링이 발생하면 이전 렌더링 이후 dependencies에 전달된 값이 변경되지 않았다면 이전에 캐시된 함수를 반환하고,
값이 변경되었다면 변경된 값을 바탕으로 다시 함수 정의를 평가하여 함수를 반환 후, 함수를 캐시처리 한다.

#### dependencies

반응형 값(props, state, 함수 내부에서 정의된 모든 변수와 함수)들의 목록. linter도구(ESlint 같은)가 React용으로 설정되어 있다면 모든 반응형 값이 dependencies에 포함되어 있지 않으면 경고를 전달한다.

### returns

초기 렌더링 : 전달한 함수를 반환한다.

후속 렌더링

- 종속성이 변경된 경우 : 이 렌더링 중에 전달된 함수를 반환한다.
- 종속성이 변경되지 않은 경우 : 이전 렌더링에서 캐시된 함수를 반환한다.

### 참고

- 컴포넌트의 최상위 레벨 또는 커스텀 hook에서만 호출할 수 있다.
  > 최상위 레벨이 최상위 스코프를 말하는 건가?
- 특별한 이유가 없는 한 캐시된 함수를 버리지 않는다. (개발 중에는 파일 변경 시마다 캐시를 버린다.)

## 응용

`useCallback`를 이용하는 것은 함수 정의를 평가를 생략함으로서 얻는 이점보다는 함수를 캐시함으로서

### 불필요한 리렌더링 막기

React에서는 컴포넌트가 렌더링 될 때 모든 자식을 재귀적으로 렌더링 한다.

하지만, React에서는 불필요한 DOM 조작을 최소화 하고싶어하기 때문에 하위 컴포넌트에 전달된 props가 변경되지 않는다면 하위 컴포넌트의 리렌더링을 생략한다.

여기 상위 컴포넌트에서 핸들러 함수를 정의하고 하위 컴포넌트로 넘겨주는 컴포넌트를 작성했다.

```jsx
function Parent() {
  const [state, setState] = useState();
  const handler = () => {};

  return (
    <div>
      <Child handler={handler} />
    </div>
  );
}
```

```jsx
function Child({ handler }) {
  return <div onClick={handler}></div>;
}
```

여기서 `Parent` 컴포넌트의 state가 변경되어 리렌더링이 발생했다고 치자.

이떄 `Child`컴포넌트는 는 모든 props가 이전 렌더링과 동일한 경우 re-render를 건너뛴다.

#### 그럼 이때 handler가 변경되지 않았으므로 하위 컴포넌트는 렌더링을 건너뛸까?

그렇지 않다.
자바스크립트에서 `함수 표현식 function () {}`, `화살표 함수 ()=>{}`는 같은 정의라도 항상 다른 함수를 생성한다.

> 객체 리터럴도 항상 새 인스턴스를 만드는 것과 유사하다.

즉, 이전 렌더링과 현재 렌더링 시에 함수 정의가 동일하더라도 하위 컴포넌트는 props로 전달된 함수를 다르다고 평가하고 다시 렌더링을 한다.

이렇게 되면 상위 컴포넌트가 리렌더링 되면 매번 새로운 함수가 전달되므로 하위 컴포넌트도 매번 불필요한 리렌더링을 실시해야 하는 문제가 생긴다.

> 리렌더링 많은 연산이 필요하지 않고 복잡하지 않은 컴포넌트라면 성능적 차이가 크지 않겠지만 복잡한 컴포넌트라면 불필요한 성능 낭비가 발생할 것이다.

#### 이때 useCallback을 사용한다면?

이럴떄 하위 컴포넌트로 전달할 함수를 `useCallback`으로 감싸준다면 종속성이 변경되지 않는 경우 캐시된 동일한 함수를 전달하게 되어 불필요한 렌더링을 줄일 수 있다!

### 상태 업데이트 시 종속성 줄이기

`useCallback`을 사용하는 함수에서 상태를 업데이트 할 떄 종속성을 줄이기 위해서 updater 함수를 사용하면 종속성을 줄일 수 있다.

```javascript
const [todos, setTodos] = useState([]);
const handleFunc = useCallback((text) => {
  const newTodo = { id: nextId++, text };
  setTodos([...todos, newTodo]);
},[todos]);
```
위 함수에서 updater 함수를 적용하면

```jsx
const [todos, setTodos] = useState([]);
const handleFunc = useCallback((text) => {
  const newTodo = { id: nextId++, text };
  setTodos(todos => [...todos,newTodo]);
},[]);
```
이렇게 종속성을 제거할 수 있다.


### useEffect가 너무 자주 발생하는 문제 해결

`useEffect`내부에서 함수를 호출하는 경우 함수도 `useEffect`의 종속성으로 추가해야 하는데 이렇게 되면 모든 렌더링 시마다 useEffect가 다시 실행되게 된다.

이를 막기 위해서 useEffect 내부에서 사용할 함수를 `useCallback`으로 캐시하여 `Effect`의 호출을 줄일 수 있다.

```js
function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  const createOptions = useCallback(() => {
    return {
      serverUrl: 'https://localhost:1234',
      roomId: roomId
    };
  }, [roomId]); // ✅ Only changes when roomId changes

  useEffect(() => {
    const options = createOptions();
    const connection = createConnection();
    connection.connect();
    return () => connection.disconnect();
  }, [createOptions]); // ✅ Only changes when createOptions changes
  // ...
```

> 하지만 함수의 종속성을 제거하는 것이 가장 나은 방법이라고 한다. useEffect 내부에서만 사용될 함수라면 useEffect 내부에서 함수를 선언하는 것이 가장 좋다고 한다.(Docs에서..)


### 커스텀 Hook 최적화
커스텀 Hook을 작성하는 경우에 반환되는 모든 함수를 래핑하는 것이 좋다고 한다. 
```js
function useRouter() {
  const { dispatch } = useContext(RouterStateContext);

  const navigate = useCallback((url) => {
    dispatch({ type: 'navigate', url });
  }, [dispatch]);

  const goBack = useCallback(() => {
    dispatch({ type: 'back' });
  }, [dispatch]);

  return {
    navigate,
    goBack,
  };
}
```
이렇게 하면 해당 커스텀 훅의 사용자가 자신의 코드를 최적화 시킬 수 있다.



# 의문

## 모든 경우에 useCallback을 사용해야만 할까?

어플리케이션이 사용자와 상호 작용이 많고, 상호 작용에 따라 렌더링이 잦게 발생한다면 유용하게 사용할 수 있다.

함수를 캐싱하는것은 몇가지 경우에만 유리하다.

- 컴포넌트의 props를 전달할 때 함수를 캐싱하여 전달하는 경우, [불필요한 렌더링 막기]
  (#불필요한-리렌더링-막기)
- 전달하려는 함수가 다른 Hook의 종속성으로 사용되어야 할때 (useEffect의 종속성이거나, 또다른 useCallback으로 감싸져 있는 함수)

공식 문서에는 이 경우 외에는 함수를 래핑하는데 큰 이점이 없다고 한다.

> 그렇지만 대부분의 함수를 useCallback으로 감싸는 것이 성능적 해는 없기 때문에 모든 함수에 useCallback을 래핑하는 경우가 있는데, 이렇게 되면 코드의 가독성이 떨어질수 있다. 또한 종속성에 매번 새로운 값이 전달되는 케이스에도 useCallback의 이점을 얻을 수 없다.

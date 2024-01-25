# useEffect

`useEffect`는 컴포넌트를 외부 시스템과 동기화 할 수 있는 React Hook이다.

> **외부 시스템**<br>
> 네트워크, 브라우저, 브라우저 DOM 등

## 주의사항

- 외부 시스템과 동기화하려는 목적이 아니라면 Effect가 불필요할 수 있다.
- Effect가 시각적인 작업들을 하고 있고, 지연이 눈에 뛰는 경우 useLayoutEffect로 대체해야 한다.

## 매개변수, 리턴값
### setup
useEffect의 로직이 포함된 함수, setup 함수는 리턴값으로 클린업 함수를 가질 수 있다.
React는 컴포넌트가 DOM에 추가되면 setup함수가 실행된다.

의존성이 변경되어 리렌더링이 발생할 때 React는 이전 값을 기준으로 cleanUp 함수를 실행하고, 새 값으로 setup 함수를 실행한다.

> useEffect 리렌더링 시 실행 순서는 클린업 => 재평가 => 셋업이 아니라, 재평가 => 클린업(이전 렌더링의 값으로) => 셋업이다.


### dependencies [optional]

## Returns
undefined를 반환한다.


## Usage

### 외부 시스템에 연동하기

```jsx
useEffect(() => {
  fetch("http://localhost/data")
    .then((res) => res.json())
    .then((data) => console.log(data));
});
```

### 데이터 패칭하기


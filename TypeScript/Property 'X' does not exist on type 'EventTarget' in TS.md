![image](https://github.com/dnrgus1127/TIL/assets/65962363/5a7571f1-44e8-404c-9bf7-11d6d2e9bb11)
TS를 사용할 때 이벤트 핸들러 내부에서 e.taret의 Property에 접근하려고 하면 타입스크립트 에러가 나오는 상황을 자주 겪었다.

매번 헷갈려서 이럴 때 마다 구글링을 했는데 까먹지 않기 위해서 정리를 해본다.

# Property 'X' does not exist on type 'EventTarget' in TS
위 에러는 EventTarget 타입 객체의 프로퍼티를 참조하려고 할 때 발생한다.

![image](https://github.com/dnrgus1127/TIL/assets/65962363/5957212e-5388-4562-a8eb-65b46872131e)

TypeScript의 타입 단언(type assertion)을 이용하여 이 문제를 해결할 수 있다고 한다.

e.target이 정확히 어떤 타입의 element인지 단언해주는 방식!

리액트로 개발중이라 리액트로 예제를 들었다.
```jsx
const documentButtonHandler : React.MouseEventHandler<HTMLButtonElement> = (e :React.MouseEvent<HTMLButtonElement> ) => {
        const target = e.target as HTMLButtonElement;
        // e.target이 HTML 버튼 엘리먼트라고 전달해준다.
        
        console.log(e.target.value)  // 에러 발생 x
    }
```

> 인라인에서 옵셔널 체이닝 연산자를 이용하면 더 간단하게 작성할 수 있다.

## 왜 EventTarget의 프로퍼티를 접근할 수 없을까?
EventTarget 인터페이스는 기본적으로 `addEventListener`,`removeEventListener`,`dispatchEvent` 와 같은 몇가지 프로퍼티만 가지고 있고,
`value` 프로퍼티같은 다른 속성들은 포함하고 있지 않다.

HTML 요소별로 가지고 있는 프로퍼티가 다르기 때문에 여러 HTML**Element가 될 수 있는 EventTarget은 몇가지 속성을 가지지 않는 것이다!


# Reference
- [bobbyhadz.com/blog](https://bobbyhadz.com/blog/typescript-property-value-not-exist-type-eventtarget)




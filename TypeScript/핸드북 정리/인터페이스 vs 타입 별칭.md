# 인터페이스 vs 타입 별칭
타입 별칭은 인터페이스와 같은 역할을 할 수 있지만 약간의 차이가 있다.

두 이름 짓기의 차이는 해당 타입을 소비하는 측면에서 다르다고 할 수 있다.

인터페이스는 어디에서나 새로운 이름으로 적용된다.

```ts
type Alias = { num: number }
interface Interface {
    num: number;
}
declare function interfaced(arg: Interface): Interface;
// 마우스를 올려보면 Interface 타입을 반환한다고 알려준다.
declare function aliased(arg: Alias): Alias;
// 하지만 타입 별칭의 경우는 객체 리터럴 타입을 반환 한다고 알려준다.
```
interfaced 함수를 호출할 때 타입스크립트는 사용자에게 해당 함수는 interface 타입을 반환한다고 알려준다.

하지만 type 별칭으로 타입의 이름을 전달한 aliased는 Alias 타입을 반한한다고 알려주는 것이 아니라 객체 리터럴 타입 `{num : number}`을 반환한다고 알려준다.


> 타입스크립트의 초기에는 interface만 타입을 extend 하거나 implement할 수 있었다.</br>
> 타입스크립트 2.7 이후부터는 교차 타입을 이용하여 타입 별칭을 확장하거나 상속 받을 수 있다.

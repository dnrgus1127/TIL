# 인터페이스

## 인터페이스 사용법

```ts
function printValue(param: { label: string }) {
  console.log(param.label);
}

interface interValue {
  label: string;
}

function printValue(param: interValue) {
  console.log(param.label);
}
```

객체를 타입 검사할 때는 최소한의 프로퍼티가 있는지 없는지를 검사한다.

이때 타입으로 지정된 요구 조건을 인터페이스로 구현하여 재사용성과 가독성을 높일 수 있다.

## 선택적 프로퍼티 (Optional Properties)

인터페이스의 프로퍼티가 어떤 조건에서만 존재하거나 없을 수도 있다.

```ts
interface value {
  color?: string;
  name?: string;
}
```

> JS의 옵셔널 체이닝 연산자와 비슷하게 생겼다.<br>

프로퍼티 이름 끝에 `?`를 붙여 사용함으로서 사용할 수 있는 프로퍼티의 범위를 나타낼 수 있다.(사용하지 않더라도 통과)

## 읽기 전용 프로퍼티

타입 프로퍼티 이름 앞에 readonly를 넣어서 지정하게 되면, 대응하는 프로퍼티가 재할당 되는 것을 막을 수 있다.

> 타입 단언은 적용할 수 있다.

```ts
interface people {
  readonly name: string;
  readonly age: number;
}
```

> 변수의 const를 프로퍼티에 적용한다고 보면 되는것 같다.

## 초과 프로퍼티 검사

객체 리터럴은 다른 변수 혹은 매개변수로 전달될 때, 초과 프로퍼티 검사를 받는다.
이 떄 객체 리터럴이 `대상 타입`이 갖고 있지 않은 프로퍼티를 가지면, 에러가 발생한다.

```ts
interface labeling {
  label: string;
}

function printLabel(labelObj: labeling) {
  console.log(labelObj.label);
}

// 초과 프로퍼티 에러 발생
printLabel({ color: "red", label: "123" });
```

위 예제에서 객체 리터럴로 생성한 객체를 `labeling` 타입을 가지는 매개 변수에 할당할때 초과 프로퍼티 검사를 받게 되고, `labeling` 인터페이스에 없는 `color`프로퍼티를 가지고 있기 때문에
에러가 발생한다.

### 해결 방법

- 타입 단언을 사용하는 방법

```ts
printLabel({ color: "red", label: "123" } as labeling);
```

- 다른 변수에 할당하기

```ts
// 이 시점에서 초과 프로퍼티 검사를 받기 때문에
let a = { color: "red", label: "123" };

// 여기서는 초과 프로퍼티 검사를 받지 않아서 에러가 나지 않는 것 같다.
printLabel(a);
```

> 다른 변수에 할당하는 방식은 두 객체 사이에 공통되는 프로퍼티가 하나 이상 있는 경우에만 사용할 수 있다.

## 함수 타입

인터페이스로 함수 타입도 지정할 수 있다.

```ts
interface func1 {
  (param1: string, param2: number): void;
  // string,number 매개변수, void 반환값
}
```

단, 매개변수의 이름 값이 같을 필요는 없고 타입만 일치하면 된다.

## 인덱서블 타입

인터페이스를 이용해서 타입을 인덱스로 기술할 수 있다.

```ts
interface StringArray {
  // 인덱스 서명
  [index: number]: string;
  // number로 색인화 하여 string을 반환한다는 인터페이스
}
```

인덱스 서명은 **문자열과 숫자** 타입만 지원한다.

두 타입의 인덱서를 모두 지원하는 것은 가능하지만, 숫자 인덱서에서 반환된 타입은 항상 문자열 인덱서에서 반환된 타입의 하위 호환이여야 한다.

```ts
class Animal {
  name: string;
}

class Dog extends Animal {
  breed: string;
}

// 에러
interface errorCase {
  [x: number]: Animal;
  [x: string]: Dog;
}
```

JS는 객체 인덱싱 시 객체의 키를 string 타입으로 변환시킨다.
즉 인덱스 100은 "100" 과 같은 결과를 나타내야 하기 때문에 위 예제에서는 에러가 발생한다.

문자열 인덱스 시그니처는 모든 프로퍼티들이 반환 타입과 일치하도록 강제한다.

```ts
interface Dictionary {
  [index: string]: number;
  name: string; // 인덱서의 하위 타입이 아니라는 에러 발생
}
// obj.name은 obj["name"]과 같기 때문에 두 반환값이 다르면 안된다.
```

## 클래스 타입

### 인터페이스 구현

Java나 C#에서 클래스에 사용하는 인터페이스를 타입스크립트에서도 사용할 수 있다.

```ts
interface Animal {
   name: string;
}

class Dog implements Animal {
  name : "Bart",
  constructor () {}
}
```

### 클래스의 스태틱과 인스턴스의 차이점

클래스가 인터페이스를 implements 할 때, 인터페이스는 클래스의 인스턴스만 검사하기 때문에 생성자, 정적 메소드 같은 스태틱 프로퍼티들은 검사에 포함되지 않는다.

```ts
interface ClockConstructor {
  new (hour: number, minute: number);
}

interface ClockInterface {
  tick();
}

const Clock: ClockConstructor = class Clock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log("beep beep");
  }
};
```

> 이 파트는 정확히 이해하지 못했음.

## 인터페이스 확장하기

클래스처럼, 인터페이스도 `extends` 키워드를 이용해 확장할 수 있다.

```ts
interface Shape {
  color: string;
}

interface Square extends Shape {
  sideLength: number;
}

// 타입 선언
let square = {} as Square
square.color = "blue";
square.sideLength = 10;
```

extends는 하나의 인터페이스뿐만 아니라 여러 인터페이스를 확장(상속?) 할 수 있다.

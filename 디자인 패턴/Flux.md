# Flux

리덕스를 이용해 전역 상태 관리를 구현하다 보니 리덕스는 Flux 디자인 패턴을 기반으로 한 라이브러리라는 글을 보게 되었다.

## 궁금증
> Flux 패턴?
> Flux 패턴의 구조
> 다른 디자인 패턴과의 차이

---

## Flux 패턴


Flux 패턴은 페이스북에서 MVC(모델,컨트롤,뷰) 모델의 단점을 보완하기 위해서 만든 아키텍처다.

> Flux 패턴은 페이스북에서 SPA(Single Page Application)을 구현하기 위해 만든 아키텍처 패턴이라고 한다. [참고](https://medium.com/@grover.vinayak0611/what-is-flux-architecture-why-facebook-used-it-and-the-comparison-with-mvc-architecture-49c01ed5d2e10)


MVC 디자인 패턴으로 개발한 경험이 없어서 단점에 대해 정확히 알고 있지는 못하지만,
MVC모델은 View가 동시에 여러 Model을 업데이트하고, Model 또한 동시에 여러 View를 업데이트 할 수 있는데, 이런 방식이 서비스가 확장될 수록 데이터의 흐름을 예측 불가능하게 만들었다.

![image](https://github.com/dnrgus1127/TIL/assets/65962363/c3d75171-194a-46f9-9645-86d15a6b9865)


### 그럼 데이터를 단방향으로만 전달되게 한다면 데이터의 흐름을 예측할 수 있지 않을까?
그래서 나온 것이 단방향 데이터 흐름을 가지는 Flux 패턴이다.

![image](https://github.com/dnrgus1127/TIL/assets/65962363/b38a1043-ed38-42bf-a443-e8d9a9e8bde5)


## 아키텍처 구조
Flux 패턴을 구성하는 주요 요소는 **action,Dispatcher,Store,View** 4가지다.

### action
액션은 어플리케이션에서 발생하는 이벤트나, 사용자의 상호 작용을 나타내는 객체다.
Action을 통해서 데이터에 어떤 변화(증가, 감소, 수정, 초기화 등)가 일어났는지 알 수 있다.

action은 Dispatcher에 의해 감지되어 store로 전달된다.

### Dispatcher
Dispatch는 action이 발생하면 이를 감지하고, action의 형태에 따라 Store의 데이터를 조작하는 역할을 한다.

Store의 데이터는 오직 Dispatcher만 조작할 수 있다

### Store
그럼 Store는 뭘까? Store는 애플리케이션의 모든 상태와 로직을 가지고 있는 객체다.

Store에는 상태 뿐만 아니라 해당 상태를 조작하는 로직(콜백 함수)도 등록되어 있다.

Dispatcher가 특정 액션을 감지해서 Store에게 특정 action이 발생했음을 알리고, store는 발생한 action에 따라서 상태 업데이트 로직을 수행한다.

state 업데이트가 완료되면 view에게 변경된 상태를 전달한다.

### View
View는 사용자가 보고 있는 인터페이스, 변경된 상태에 대한 표현과 사용자의 입력에 대해 반응하여 새로운 action을 발생시킨다.

> 리액트의 컴포넌트 같은 개념이라고 한다. (페이스북이 React와 Flux 패턴 두가지를 만들었기 때문에 주로 리액트의 컴포넌트로 나타낸다.)


> view가 직접 상태를 조작하지 않고, action을 통해서 상태 변경을 유발하기 때문에 애플리케이션의 상태 변화가 예측 가능하고 유지보수에 이점을 얻을 수 있다.






# Reference
- [What is Flux Architecture? Why Facebook used it? and the Comparison with MVC architecture.](https://medium.com/@grover.vinayak0611/what-is-flux-architecture-why-facebook-used-it-and-the-comparison-with-mvc-architecture-49c01ed5d2e1)

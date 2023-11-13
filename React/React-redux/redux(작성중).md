# Redux
리덕스는 애플리케이션의 상태 관리를 도와줄 수 있는 오픈 소스 "자바 스크립트" 라이브러리이다.

> 이름 때문인지 Redux가 React에서만 사용할 수 있는 라이브러리인줄 착각하는 경우도 있다. 처음엔 나도 그랬다.

리덕스는 상태의 관리를 store를 기반으로 중앙 집중식으로 관리해주며, 상태가 일관성 있고, 서로 다른 환경(네이티브,서버,클라이언트)에서도 작동할 수 있게 해준다.
또한 redux-devtools를 사용하여 상태가 언제, 어디서, 어떻게 바뀌었는지 쉽게 추적할 수 있는 기능을 제공한다.

## Redux Toolkit   
Redux Toolkit(RTK)은 리덕스 개발진들이 추천하는 Redux 로직 작성 툴이다. 
```bash
npm install @reduxjs/tookit
```
> RTK는 저장소 준비, 리듀서 생산과 불변 수정 로직 작성, 상태 "조각" 전부를 한번에 작성 등 일반적인 작업들을 단순화해주는 유틸리티를 포함하고 있습니다.
> // redux docs

## React + Redux = react-redux
리덕스를 리액트에 바인딩 하여 사용할 수 있는 react-redux를 사용하면 리액트에서 redux를 사용할 수 있고, 관련 커스텀 훅들을 이용할 수 있다.
```bash
npm install react-redux
```




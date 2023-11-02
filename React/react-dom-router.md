# react-dom-router
react와 함께 자주 사용되는 ** 라이브러리

## 왜 사용해야 할까?
`<a link/>` 태그가 존재함에도 react-dom-router를 사용해야 하는 이유는 **SPA** UX 향상을 목적으로 한다.

### a 태그를 사용하는 경우
- 페이지 전체가 이동하게 되고 화면 깜빡임이 발생한다.

### SPA의 렌더링 방식을 이용하여 모든 페이지를 렌더링 한다.
- 특정 페이지에 대한 즐겨찾기를 등록할 수 없다.
   -> React 앱에 대해서 즐겨찾기를 등록하더라도 항상 첫 렌더링 페이지가 표시된다.
- 뒤로가기, 새로고침시에도 위와 같은 문제가 발생한다.
- SEO에서 문제가 생길 수도 있다고 한다.

### react-dom-router를 사용하면
- 화면 깜빡임이 발생하지 않는다.
- 즐겨찾기, 뒤로가기, 새로고침 시에도 첫 렌더링 화면이 표시되지 않고 원하는 페이지를 렌더링 해줄 수 있다.



## 사용법 (react-router-dom v6.4버전 이상)
> v6.4로 업데이트 되면서 문법이 좀 변경되었다고 한다.
> 새로운 데이터 API를 지원하는 새로운 라우터들이 추가되었다.

### 설치
```bash
npm install react-router-dom
```

## 라우터 종류
### [createBrowserRouter](https://reactrouter.com/en/main/routers/create-browser-router)
 보통의 웹 프로젝트에서 사용하기



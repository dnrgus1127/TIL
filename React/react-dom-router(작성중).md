# react-dom-router

react와 함께 자주 사용되는 클라우트 라우팅 라이브러리

## 왜 사용해야 할까?

`<a link/>` 태그가 존재함에도 react-dom-router를 사용해야 하는 이유는 **SPA** UX 향상을 목적으로 한다.

### a 태그를 사용하는 경우

- 페이지 전체가 이동하게 되고 화면 깜빡임이 발생한다.

### SPA의 렌더링 방식을 이용하여 모든 페이지를 렌더링 한다.

- 특정 페이지에 대한 즐겨찾기를 등록할 수 없다.
  -> React 앱에 대해서 즐겨찾기를 등록하더라도 항상 첫 렌더링 페이지가 표시된다.
- 뒤로가기, 새로고침시에도 위와 같은 문제가 발생한다.
- SEO에서 문제가 생길 수도 있다고 한다.

### react-dom-router

앱이 서버에 다른 문서를 요청하지 않고, 링크 클릭을 통해서 URL을 업데이트 할 수 있다.

- 화면 깜빡임이 발생하지 않는다 (애니메이션 등을 이용한 UX 향상도 기대할 수 있다).
- 즐겨찾기, 뒤로가기, 새로고침 시에도 첫 렌더링 화면이 표시되지 않고 원하는 페이지를 렌더링 해줄 수 있다.
- 브라우저가 새로운 문서를 요청할 필요가 없기 때문에 CSS및 JS를 다시 평가하지 않아도 되어 성능 향상이 있다.

---

# 사용법 (react-router-dom v6.4버전 이상)

> v6.4로 업데이트 되면서 문법이 좀 변경되었다고 한다.
> 새로운 데이터 API를 지원하는 새로운 라우터들이 추가되었다.

## 설치

```bash
npm install react-router-dom
```

## 라우터 생성

라우터를 앱과 연결하는 과정이다.

> react-dom-router는 몇가지 [라우터 종류](#라우터-종류)를 제공한다.

우선 앱에서 사용할 라우터를 생성하고 `RouterProvider`를 이용해서 앱을 감싸줘야 한다.

```jsx
import * as React from "react";
import * as ReactDOM from "react-dom/client";
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import "./index.css";

// browserRouter 생성
const router = createBrowserRouter([
  {
    path: "/",
    element: <div>Hello world!</div>,
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

[createBrowserRouter](#createbrowserrouter) 함수를 이용해서 앱에서 사용할 BrowserRouter를 생성하고
`RouterProvider`를 이용해서 React APP을 감싸준다.

이제 루트 주소("/")에 접속하게 되면 router에 element로 전달된

```html
<div>Hello World!</div>
```

이 표시된다.

## 루트 Route 연결

이제 router의 element에 루트 레이아웃으로 사용할 컴포넌트를 연결해주자

```jsx
import Root from "./routes/root";

const router = createBrowserRouter([
  {
    path: "/",
    element: <App />,
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

나는 react의 `App` 컴포넌트를 연결해줬다.

이제 App 컴포넌트는 루트 Route로 내부에서 route 조작을 할 수 있다.

## 에러 페이지 연결

[createBrowserRouter](#createbrowserrouter)의 옵션으로 errorElement를 설정하면
렌더링, 데이터 오류 등의 과정을 수행하다가 앱에서 오류가 발생할 시 보여주는 화면을 지정할 수 있다.

```jsx
const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
  },
]);
```

## 중첩 경로 사용하기

루트 route의 내부에서 경로를 나눠서 다른 요소를 렌더링할 수도 있다.

```jsx
const router = createBrowserRouter([
  {
    path: "/", // https://localhost:port/
    element: <Root />,
    errorElement: <ErrorPage />,
    children: [
      {
        path: "setting/:settingId", // https://localhost:port/setting/2
        element: <Setting />,
      },
    ],
  },
]);
```

이렇게 children으로 루트 route 내부에서 중첩적으로 경로를 가질 수 있다.

그럼 children으로 전달된 컴포넌트는 Root컴포넌트의 어디에 렌더링 되는걸까?

### Outlet

렌더링할 위치를 지정해주기 위해서 `Outlet`을 사용한다.

```jsx
import { Outlet } from "react-router-dom";

export default function Root() {
  return (
    <>
      <div>
        <Outlet />
        // 이 위치에 children 요소가 렌더링 된다.
      </div>
    </>
  );
}
```

> 경로가 일치하면 Outlet 위치에 자식 요소가 렌더링 된다.

## 경로 이동

`a` 태그를 이용해서 하이퍼링크를 통해 이동하게 되면 route는 적용되지 않는다.

`react-router-dom`은 `Link` 컴포넌트를 제공한다.

```jsx
<Link to={`setting/1`}>
```

Link 태그를 이용해서 URL을 업데이트 하게 되면 서버에 새로운 문서를 요청하지 않고도 새로운 UI를 렌더링 할 수 있다.

## 데이터 API

데이터는 페이지를 렌더링할 때 결합되어서 사용되는 경우가 많다.

react-router-dom의 데이터 API를 이용하면 컴포넌트가 렌더링 되기 직전에 데이터를 로드할수 있다.

```jsx
export async function loader() {
  // 비동기 데이터 로드 과정
  const res = await fetch(
    `https://api.github.com/repos/dnrgus1127/${repo}/readme`
  );
  const data = await res.json();
  return { data };
}
```

로더 함수(데이터를 불러오는 비동기 함수)를 작성하고

```jsx
const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    loader: loader, // 로더 함수 지정
  },
]);
```

라우터에게 전달해주면 해당 경로의 element를 렌더링 하기 전에
loader함수가 먼저 실행되어서 데이터를 먼저 가져온다.

```jsx
import { useLoaderData } from "react-router-dom";

export default function Root() {
  const { data } = useLoaderData();
}
```

이후 컴포넌트 내부에서 `useLoaderData`함수를 사용해서 loader함수에서 미리 가져온 데이터를 사용할 수 있다.

-----------
작성중
-------

## 라우터 종류

### [createBrowserRouter](https://reactrouter.com/en/main/routers/create-browser-router)

> React Router 공식 문서에서는 React Router 프로젝트에서 기본적으로 이 라우터를 사용하기를 권장하고 있다.

"DOM History API"를 이용하여 URL을 업데이트하고 History Stack을 관리하고,
v6.4에서 지원하는 Data API인 `loaders`,`actions`,`fetchers` 등을 사용할 수 있다.

```jsx
import * as React from "react";
import * as ReactDOM from "react-dom";
import { createBrowserRouter, RouterProvider } from "react-router-dom";

import Root, { rootLoader } from "./routes/root";
import Team, { teamLoader } from "./routes/team";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    loader: rootLoader,
    children: [
      {
        path: "team",
        element: <Team />,
        loader: teamLoader,
      },
    ],
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <RouterProvider router={router} />
);
```

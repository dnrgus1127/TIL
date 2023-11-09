# react-router-dom 6.4 
v6.4 이상에서 data api를 사용하기 위해서는 createRouter 함수를 이용해서 라우터를 생성한다.

```jsx
// 예시
const router = createBrowserRouter([
  {
    path: '/',
    element: <App />,
    children: [
      {
        path: '',
        element: <Home />,
      },
      {
        path: 'about',
        element: <About />,
      },
    ],
  },
]);
```

이 때 children 요소가 부모 컴포넌트의 어느 부분에 들어가야 할지 정해주는 것이 바로 `<Outlet />
`이다.


# <Outlet />

outlet은 부모 요소의 jsx 부분에 추가하여 사용하면 된다

```jsx
function Parent() {
return (
  <div>
    <Oulet/>
  </div>
)
```

이렇게 사용한 outlet의 자리에는 자식 요소의 element로 전달한 컴포넌트가 전달된다.


## outlet에 props 넘겨주기
만약 자식컴포넌트가 props를 전달받아야 한다면 어떻게 해야할까?

이럴 때는 Outlet 컴포넌트의 context 속성으로 넘겨주고자 하는 값을 전달하고, 자식 요소에서 `useOutletContext()` 훅을 사용해야 한다.


```jsx
// 부모 요소
function Parent() {
return (
  <div>
    <Oulet context={{data}}/>
  </div>
)


// 자식 경로 컴포넌트
import {useOutletContext} from "react-router-dom";
function Child() {
const {data} = useOutletContext();
return (
  <div>{data}</div>
```

## 타입스크립트의 경우
타입스크립트의 경우 부모 요소에서 context value를 참조하는 것에 대한 커스텀 훅을 만들어서 제공하는 것이 좋다고 한다.
이렇게 하면 해당 context를 소비하는 측면에서 보다 쉽게 타입에 대한 정보를 얻을 수 있다.
```ts
import * as React from "react";
import type { User } from "./types";
import { Outlet, useOutletContext } from "react-router-dom";

type ContextType = { user: User | null };

export default function Dashboard() {
  const [user, setUser] = React.useState<User | null>(null);

  return (
    <div>
      <h1>Dashboard</h1>
      <Outlet context={{ user } satisfies ContextType} />
    </div>
  );
}

export function useUser() {
  return useOutletContext<ContextType>();
}
```



```ts
import { useUser } from "../dashboard";

export default function DashboardMessages() {
  const { user } = useUser();
  return (
    <div>
      <h2>Messages</h2>
      <p>Hello, {user.name}!</p>
    </div>
  );
}
```


> 타입스크립트에 적용하려다 막혀서 찾아본 부분인데 커스텀 훅을 이런 식으로도 사용할 수 있다는 것이 신기했다.

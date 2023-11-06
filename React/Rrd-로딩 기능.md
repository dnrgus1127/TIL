# [react-router-dom](https://github.com/dnrgus1127/TIL/blob/main/React/react-dom-router(%EC%9E%91%EC%84%B1%EC%A4%91).md)

리액트 라우터v6.4+의 data Api인 loader를 사용할 때 로딩 중 기능에 관하여.

## loader
react-router-dom v6.4에서 도입된 Data api인 loader는 페이지가 이동될 때 컴포넌트 렌더링 직전에 호출된다.

기존에는 `useEffect`를 이용하여 fetch함수로 서버에 데이터를 요청하는 방식을 사용했는데
`useEffect`에 전달된 함수는 컴포넌트의 렌더링이 끝난 후에 실행되기 때문에 데이터가 초기화 되어 있지 않으면 오류가 발생한다.
```jsx
export default function UserPage() {
    const [data, setData] = useState();
    useEffect(() => {
        fetch("https://api.github.com/users/dnrgus1127")
            .then(res => res.json())
            .then(data => setData(data))
    })
  return (
    <div>{data.login}</div>
  )
// useEffect 실행 전 최초 렌더링에서 data에는 login 프로퍼티가 존재하지 않는다고 에러가 발생한다.
}
```
그렇기 때문에 jsx에서 데이터가 존재하는지에 대한 검증을 하거나, 로딩 상태를 구현해서 사용해야 한다.
```jsx
<div>{data&& data.login}<div>
```
```jsx
// react query 예시
function UserPage() {
  const {data,isLoading} = useQuery({queryFn : async () => {
  const res = await fetch("https://api.github.com/users/dnrgus1127");
  const data = await res.json();
  return data;
}, queryKey : ["user"])
  }


if (isLoading) return <div>로딩중 ... </div>
return <div>
  ... jsx component
</div>
```

하지만 React-router-dom의 loader를 사용하면 컴포넌트의 렌더링 이전에 데이터를 요청하기 때문에 컴포넌트에게 해당 데이터가 존재하는지에 대하여 검증할 필요가 없다.
컴포넌트에는 데이터가 지금 로딩중이라는 것을 알릴 필요가 없다는 것이다.

하지만, 앱의 사용자 입장에서는 페이지 전환중에 데이터를 불러오는데 지연시간이 발생하면 이 동안 어떤 일이 발생하는지 알려줘야 할 필요가 있다.

## useNavigation
리액트의 내장 훅인 `useNavigation`을 사용하면 데이터가 로딩되고 있는지를 확인하여 사용자에게 로딩중이라는 것을 알릴 수 있다.

function RootLayout () {
  const navigation = useNavigation();

  return (  
  <>
    {navigation.state === "loading" && <Loading/>
    <Contents/>
  </>
  
  )
}





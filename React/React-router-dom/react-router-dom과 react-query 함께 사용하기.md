# React-router-dom + React Query

router의 data API 인 loader를 사용하여 데이터를 초기에 가져오는(렌더링 전에) 역할을 수행할 수는 있지만.

데이터가 캐시되지 않아서 불필요한 데이터 요청이 많이 발생할 수 있다는 단점이 존재한다.

데이터 캐시 기능을 위해서 React Query를 도입하고 router와 함께 사용해 봤다.

## queryClient를 로더에 전달

일단 로더에서 useQuery를 호출하는 방법은 불가능하다.

우선 useQuery는 react Hook이기 때문에 리액트 컴포넌트가 아닌 loader 함수에는 호출할 수 없다.

```jsx
import ProjectsContainer, {
  loader as projectLoader,
} from "component/ProjectsContainer";

const queryClient = new QueryClient();

const router = createBrowserRouter([
  {
    path: "/",
    element: <App />,
    children: [
      {
        path: "/gitRepo",
        element: <ProjectsContainer />,
        loader: projectLoader(queryClient),
        // queryClient 를 인자로 넘겨준다.
      },
    ],
  },
]);
```

그래서 load 함수의 인자로 queryClient 객체를 전달하고,

```jsx
// Query의 인자로 전달될 객체 생성
const projectQuery = (repo: string) => ({
  queryKey: ["project", repo],
  queryFn: async () => {
    // 데이터 fetch 함수 
    const res = await fetch(
      `https://api.github.com/repos/dnrgus1127/${repo}/readme`
    );
    const data = await res.json();
    return data;
  },
});

export const loader = (queryClient: any) => async () => {
  const query = projectQuery("colorProject");

  return (
    queryClient.getQueryData(query.queryKey) ??
    (await queryClient.fetchQuery(query))
  );
};
```
loader함수는 인수로 전달받은 queryClient를 이용해서 데이터를 불러온다.

이제 loader는 렌더링 되기 전에 호출되어서 데이터를 미리 불러오게 된다.(캐시된 데이터가 없을 떄)

```jsx
interface ProjectsQueryData  {
    name: string;
  };
  
  export default function ProjectsContainer(): JSX.Element {
    const { data : project } = useQuery<ProjectsQueryData,Error>(
      projectQuery("colorProject")
    );

    return (
        <ProjectsTemplate readme={project!.name} />
    );
  }
```
이제 데이터를 사용하는 컴포넌트의 useQuery는 두가지 역할을 한다.
1. loader가 이미 데이터를 요청해서 캐싱해둔 데이터를 가져오는것 (서버로 요청 x)
2. 데이터 캐시가 만료되었을 때 서버에서 다시 데이터를 요청하는 것.


전체적인 렌더링 과정을 보면

1. URL이 변경되어 ProjectContainer를 렌더해야한다
2. 렌더링 되기 전에 loader 함수가 실행된다.
3. loader함수는 서버에게 데이터를 요청하고 응답받은 데이터를 캐시에 저장한다.
4. ProjectContainer 컴포넌트가 렌더링 되기 시작한다.
5. ProjectContainer 내부의 useQuery는 캐시 되어있는 데이터를 불러온다.
6. 시간이 지나서 캐시가 만료되고 다시 데이터를 불러와야 할 일이 발생한다.
7. useQuery가 서버로 다시 데이터를 요청한다.





## 참고

- https://tkdodo.eu/blog/react-query-meets-react-router#fetching-too-often

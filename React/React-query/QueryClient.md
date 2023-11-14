[TanstackQuery v4 - QueryClient](https://tanstack.com/query/v4/docs/react/reference/QueryClient#queryclientfetchquery)

# QueryClient 
리액트 쿼리의 queryClient를 사용하여 캐시와 상호 작용을 할 수있다.


이하는 메소드중 일부를 정리했다. ( 공식 문서 참고 )

## fetchQuery
쿼리를 통해 데이터를 가져오고 캐시할 수 있는 비동기 메서드.

비동기 resolve의 결과로 데이터 혹은 에러를 반환한다.

>  prefetchQuery를 사용하면 데이터를 반환 없이 바로 먼저 캐시 처리하여 사용할 수 있다.

캐시된 쿼리가 존재할때, 데이터가 유효하지 않거나 지정된 'staleTime'보다 오래된 경우 최신 데이터를 가져오려고 시도하고,
그게 아니라면 캐시된 데이터를 가져온다.

메서드의 옵션으로 `staleTime`을 전달할 수 있고(ms) 지정한 시간보다 오래된 데이터인 경우 새로운 데이터를 불러온다.


## getQueryData
getQueryData는 기존에 쿼리에 캐시되어 있는 데이터를 가져오는데 사용할 수 있다.
캐시된 쿼리가 존재하지 않으면 undefined를 반환한다.

> 한번에 여러 쿼리의 캐시된 데이터를 가져오기 위한 getQueriesData도 존재한다.


## setQueryData
setQueryData는 커리된 캐시의 데이터를 수동적으로 즉시 업데이트 할 수 있는 동기 함수이다.

setQueryData는 이미 동기적으로 사용할수 있는 데이터가 저장되어 있다는 것을 가정하고 사용한다.

> 북마크 등을 구현할 때 데이터의 refetch를 기다리지 않고 즉각적으로 북마크 UI를 업데이트 할때 사용하는 글도 있었다.

## getQueryState
쿼리키를 전달하여, 현재 쿼리의 상태를 가져오는데 사용할 수 있는 동기 함수이다. 
> 쿼리가 존재하지 않으면 undefined를 반환한다.


## invalidateQueries
캐시된 쿼리(쿼리들)을 무효화시키고 refetch하는데 사용할 수 있는 메소드다.

옵션으로 전달된 쿼리키(혹은 쿼리필터)에 충족하는 쿼리들을 전부 무효화 시키고 다시 요청한다.
`refetchType` 옵션을 통해서 쿼리를 무효화만 시키고 요청은 하지 않거나, 이미 비활성화 되있던 쿼리들도 다시 패치하는 옵션 등이 있다.

### refetchType
- active : 기본 (사용되고 있는 쿼리만)
- inactive : 사용되지 않는 쿼리만
- all :  사용되고 있든 아니든 모든 쿼리(쿼리키에 해당하는)
- none : refetch 발생 없이 state만 비활성화 상태로 변경

## 옵션 설정

### getDefaultOptions
### setDefaultOptions

### getQueryDefaults
### setQueryDefaults

### getMutationDefaults
### setMutationDefaults


## 캐시 확인
### getQueryCache
### getMutationCache


## 기타
- cancelQuery
- removeQueries
- resetQueries
- isFetching, isMutating,
- getLogger
- 













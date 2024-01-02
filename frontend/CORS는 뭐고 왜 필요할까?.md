CORS : 교차 출처 리소스 공유

포트폴리오 배포 중에 REST API를 이용할때 CORS문제 때문에 며칠 애를 먹었다.<br/>
결국 API를 요청하는 프록시 서버를 구성했지만, 애를 먹인 원인중 하나인 CORS에 대해서 잘 몰랐던것 같아서 MDN을 천천히 읽으며 정리해봤다.

# Same-Origin Policy : 동일 출처 정책

우선 CORS를 이해하기에 먼저 왜 CORS가 필요한지 부터 짚고 넘어가자.

우리가 사용하고 있는 최신(비교적) **브라우저**에서는 웹 보안을 위해서 브라우저 위에서 동작하는 JS 코드가 **다른 출처(origin)** 의 리소스와 자원을 주고받는 것을 제한하는 정책인 **동일 출처 정책**을 따르고 있다.

## 다른 출처?

여기서 말하는 다른 출처란 프로토콜, 호스트, 포트 번호 중 하나라도 다른 경우를 말한다.
내가 https://woogihyun.com (443포트)에서 웹 문서를 전달받았다고 생각해보자.

아래의 URI중 어떤 경우가 다른 출처에 해당할까?

1. http://woogihyun.com
2. https://wook.com
3. https://woogihyun.com (8080포트)

정답은 모두 다 해당한다. 프로토콜(http/s), 호스트(도메인), 포트 번호 중 하나다로 다르면 다른 출처로 인식하기 떄문이다.

---

동일 출처 정책이란

다른 출처의 리소스와 상호작용을 제한함으로서 공격자의 공격이나 리소스 유출을 방지하는 보안 매커니즘이다.

하지만 우리는 특정 사이트에서 다른 리소스로 자원을 요청해야할 필요가 있다.

그렇다면 보안을 위해서 요청을 포기하고 서비스 개발을 중단해야 할까?

# Cross-origin Resource sharing : CORS

CORS가 바로 위의 동일 출처 정책을 피해서 다른 출처 사이의 자원을 공유할 수 있게 만들어주는 매커니즘이다.

CORS 매커니즘은 HTTP요청에 Access-Control-Allow-Origin 헤더를 추가하여 교차 출처의 리소스에 접근할 수 있게 해주는 것이다.

서버에서 리소를 HTTP로 전달할 때 Aceess-Control-Allow-Origin 헤더에 교차 출처 리소스 요청을 허가하고자 하는 출처를 지정하고, 교차 출처 리소스를 요청한 출처가 만약 해당 헤더에 포함된 출처라면 리소스를 읽는 것을 허용하는 것이다.

> 사실 웹 개발을 하면서 나도 모르게 CORS는 많은 곳에서 쓰이고 있었다.
> 폰트 디자인을 적용하기위해서 사용하는 웹 폰트들 (@font-face 등)을 예로 들수 있다.
> ![Snipaste_2023-12-29_15-30-43](https://github.com/dnrgus1127/TIL/assets/65962363/5c06c02b-14ab-4509-ad2a-7b223f10b498)

1. A에서 B로 리소스를 요청한다. (A와 B는 다른 출처를 가지므로 이는 교차 출처 리소스 요청에 해당한다.)

- 이때 HTTP 요청 헤더에는 **Origin : A**가 포함된다. 즉 이 요청은 A에서 왔다는 것이다.

2. B는 리소스의 응답헤더에 서버의 정책에 맞는 Access-Controll-Allow-Origin헤더를 다시 전송한다.

- 서버의 CORS 정책에 A 사이트가 포함되어 있다면 Access-Controll-Allow-Origin 헤더에 A를 포함하여 응답하고, 모든 도메인에서 교차 출처를 허용하고자 하면 \*를 담아서 반환한다.

```http
// A사이트만 허용할 경우
Access-Control-Allow-Origin: https://woogihyun.com (A)
// 모든 도메인에서 접근할 수 있도록 허용할 경우
Access-Control-Allow-Origin: *
```

## Simple request와 Preflight request

브라우저는 CORS 정책을 검증할 때 특정 조건에 따라 두가지 방식으로 요청이 나뉜다.

### 1.Simple request

첫번 째로 단순 요청이다. 이는 다음 조건의 요청의 경우에 해당한다.

- 메서드 : GET, HEAD, POST 중 하나인 경우
- 헤더 : UserAgent(웹 브라우저 등)가 자동으로 설정한 헤더
- Content-Type : `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain` 인 경우

이런 경우는 위에서 설명한 방식대로 요청헤더의 Origin과 응답 헤더의 Access-Control-Allow-Origin 두가지만 이용해서 CORS 검증을 하는 방식이다.

### 2.Preflight request

두번째 방식인 Preflight request는 *1.Simple request*에 해당하지 않는 경우로, 이 요청들은 요청 자체로 서버의 리소스에 영향을 줄 수 있다.

> CORS 에러가 발생할 때 콘솔에 preflight request관련 에러가 발생했었는데 그게 왜 필요한 것인지 이해했다.

그래서 브라우저에서 사전 요청을 통해 해당 요청이 안전한 전송인지(서버에서 허용한 방식인지) 미리 검증하는 것이다.

> API요청을 할 때 header에 Authorization을 넣으면 CORS에러가 발생하고 넣지 않으면 정상적인 요청이 된 적이 있었다. 똑같이 교차 출처인데 왜 CORS 에러가 발생하는지 알지 못했었는데 이 부분을 공부하면서 해당 문제에 대한 이유를 이젠 알것 같다.

> 사전 요청은 개발자가 작성할 필요없이 브라우저가 자동으로 요청한다.

우선 OPTIONS 요청으로 CORS 요청이 필요한 http에 대한 정보를 먼저 보낸다.

![image](https://github.com/dnrgus1127/TIL/assets/65962363/510da7eb-39ac-4a58-8813-414e937f8f4f)

- OPTIONS 요청의 헤더로 실제 보내고자 하는 요청에 대한 Origin, Method, Header 정보를 보낸다.

![image](https://github.com/dnrgus1127/TIL/assets/65962363/74a03987-4d41-4c47-a868-6393e7840cdd)

- OPTIONS 요청에 대한 응답으로 서버가 지원하는 메소드와 헤더, Preflight 요청에 대한 캐시 시간 등을 전송한다.

![image](https://github.com/dnrgus1127/TIL/assets/65962363/d1edbb1f-267b-4a27-852c-cea1c403486e)

- 이렇게 preflight 요청을 통해서 CORS 요청이 허용된다면 실제 요청을 진행하교 교차 출처에 대한 리소스를 전달받는다!

# 여담

CORS에러는 프론트건 백엔드건 잘 이해하고 있는것이 중요하다고 했다. 이번에 CORS를 정리하다보니 이제것 발생했던 CORS에러들에 대한 궁금증이 거의 다 풀리는것 같다. CORS를 먼저 공부했더라면 많은 시간을 아낄 수 있었을것 같다.

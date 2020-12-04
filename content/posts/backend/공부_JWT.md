---
title: "JWT(1) - JWT(JSON Web Token)란 무엇인가?"
date: 2020-02-06T21:42:05+09:00
categories: ["backend"]
---

# JWT(JSON Web Token)
***

프로젝트를 진행 중 모바일에서 로그인 API를 제공해야 될 일이 생겼다. 기존에 웹에서 프로젝트를 할 때는 session 정보를 이용하여 로그인 기능을 제공하였는데 모바일 환경에서 JWT를 이용하여 로그인 API를 제공한다는 것을 알게되고 실제 프로젝트에 적용해봤다. 프로젝트가 끝난 이후 JWT에 대헤서 다시 한번 상기시키고 문서로 개념을 정리해보려고 한다.

<br><br>

### 1. JWT란?

> JWT란 JSON Web Token의 약자로 Json 포맷을 이용하여 Claim(사용자 정보나 데이터 속성 등을 의미) 정보를 디지털 서명 하고 이후 비밀 서명 키로 검증하는 Web Token입니다.

- 두 개체에서 JSON 객체를 이용해 Self-contained 방식으로 정보를 안전한게 전달

- JWT는 토큰 자체에 정보를 가지고 있다.

- 회원 인증, 정보 전달에 주로 사용


위의 말도 어렵다면 쉽게 정보를 가진 토큰을 통한 인증을 위해 사용된다고 이해하면 좋다.

<br><br>

### 2. JWT 구조

JWT는  **Header, Payload, Signature** 로 3부분으로 JSON 형태를 Base64로 인코딩하여 이루져있다.  

![image](https://user-images.githubusercontent.com/50758600/74012466-aabf2b80-49cd-11ea-964d-c18739797ada.png)

- **Header**

  - 토큰의 첫번째 파트이다.

  - 전형적으로 2가지 파트로(alg 와 typ)으로 구성되어있다.

    - alg : 알고리즘 방식을 지정하며, 서명(Signature) 및 토큰 검증에 사용

    - typ : 토큰의 타입

- **Payload**

  - 토큰의 두번째 파트이고 Claim 정보를 담고있다. Claim은 총 3가지 파트로 나누어진다.

  - Claim

    - Registered claims(등록된 클레임)

      토큰 정보를 표현하기 위해 이미 정해진 종류의 데이트들이다. 모두 선택적으로 작성이 가능하며 사용할 것을 권장한다. 또한 JWT를 간결하게 위해 key는 모두 3글자이다.

        - iss : 토큰 발급자(issuer)

        - sub : 토큰 제목(subject)

        - aud : 토큰 대상자(audience)

        - exp : 토큰 만료 시간(expiration), NumericDate 형식으로 되어 있어야 함 ex) 1480849147370

        - nbf : 토큰 활성 날짜(not before), 이 날이 지나기 전의 토큰은 활성화되지 않음

        - iat : 토큰 발급 시간(issued at), 토큰 발급 이후의 경과 시간을 알 수 있음

        - jti : JWT 토큰 식별자(JWT ID), 중복 방지를 위해 사용하며 일회성 토큰(Access Token) 등에 사용      

      - 토큰

    - Public claims(공개 클레임)

      충돌 방지를 위해 공개된 클레임이다. 보통 URI형식으로 짓는다.

    - Private claims(비공개 클레임)

      서버와 클라이언트간에 협의된 클레임 이름이다.

  Payload의 예제로는 다음과 같습니다.

  ~~~javascript
  { // 실제 프로젝트에 적용한 AccessToken 예제
    "sub": "AccessToken",
    "iat": 1581231911,
    "exp": 1581232271,
    "uid": "21"
  }
  ~~~

- **Verify Signature**

  Signature(서명)은 토큰을 인코딩하거나 유효성 검증을 할 때 사용하는 고유한 암호화 코드이다. Signature는 Header와 Payload의 값을 인코딩(BASE64)하고, 이코딩한 값을 비밀 키를 이용해 Header에 정의한 알고리즘으로 해싱을 하고, 이 값을 BASE64로 인코딩하여 생산한다.

  ~~~js
  HMACSHA256(
    base64UrlEncode(header) + "." +
    base64UrlEncode(payload),
    your-256-bit-secret
  ~~~

<br><br>

### 3. JWT 장점과 단점

**장점**

- JWT는 발급 후 토큰 검증만 하면 되기 때문에 저장소가 필요없다.

- 세션 정보를 유지할 필요가 없어, 로드밸런싱 환경에서 유리하다.

- 토큰 기반으로 하는 다른 인증 시스템에 접근이 가능하다.

- 정보를 가지고 있어 별도의 쿼리문을 줄일 수 있다.


**단점**

- JWT는 한 번 발급되면 유효기간이 지나기 전까지 정보들을 탈취할 수 있다.

  - Access Token과 Refresh Token을 이용해 피해를 줄일 수 있다.

- payload 정보가 제한적이다.

  - JWT는 서명에 의해 변조되지 않음을 보장할 순 있지만 데이터를 다른 사람이 볼 수 있으므로 Header나 Payload에 private한 정보는 넣지 않을 것을 권장합니다.


<br><br>

### 4. Access Token & Refresh Token

앞서 말한 JWT의 단점 중에 토큰을 탈취 당할 경우 보안에 취약하다는 단점이 있었다. 토큰의 유효기간은 길 수록 토큰이 탈취 당했을 떄 보안에 취약하게 된다. 그렇다고 토큰의 유효기간이 짧아지면 새 토큰을 발급받기 위해 로그인을 자주 시도해야 되게 된다. 이러한 문제점에서 해결 대안으로 나온 것이 **Access Token과 Refresh Token** 이다. 이 개념은 OAuth에서 나왔는데 이는 추후에 공부해도록 하겠다.

Access Token과 Refresh Token 모두 JWT 형태입니다. 로그인 완료되었을 떄 유효기간이 짧은 Access Token과 비교적 유효기간이 긴 Refresh Token을 발행합니다. Refresh Token은 Access Token의 유효기간이 만료되었을 떄 새로 Token을 발급해주는 열쇠가 됩니다.

에를 들어보면

- 가정) Refresh Token은 유효기간이 2주, Access Token은 유효기간이 1시간이다.

  1. 사용자는 로그인을 하여 Refresh,Access Token을 받는다

  2. 사용자는 API 요청을 하다가 1시간이 지나면 Access Token이 만료된다.

  3. Refresh Token의 유효기간이 지나지 않았다면 새롭게 Access Token을 발급받는다.

  4. Refresh Token은 2주가 지나면 유효기간이 만료된다.

  5. 사용자는 새로 로그인을 한다.

  위와 같은 과정을 통해 기존에 유효기간이 길 수록 보안에 취약한 Access Token으 단점을 보완하고, 유효기간을 줄일 경우 로그인을 자주 시도해야 된다는 단점을 Refresh Token으로 보완한 것을 알 수 있습니다.

위의 예시를 조금더 자세하게 그림으로 설명하면 다음과 같습니다.

![image2](https://t1.daumcdn.net/cfile/tistory/99DB8C475B5CA1C936)

- 여기서 9~11번은 무조건적인 상황은 아니다. 클라이언트에서 Access Token의 Payload를 통해 유효기간을 알기 떄문에 API 요청전에 토큰이 만료되면 클라이언트에서 토큰 재발급을 요청할수있다.

결론적으로 Refresh Token을 통해 Access Token을 단독적으로 사용했을 때보다 보안에 좋아졌다는 점을 알 수 있다. 그러나 Access Token이 만료될 떄마다 새롭게 발급받기 위해 HTTP 요청이 많아진다는 단점이 있습니다.

<br><br>

### 5. 마무리

이번 문서를 통해 JWT를 다시 정리하며 개념을 확고히 잡은 것 같습니다. 혹여나 잘못된 정보가 있으면 언제든지 댓글 달아주시면 감사하겠습니다. 다음 **JWT 예제** 포스팅에서 뵙도록 하겠습니다 :D

<br><br><br><br><br>

참고 자료

- https://jwt.io/introduction

- https://mangkyu.tistory.com/56

- https://elfinlas.github.io/2018/08/12/whatisjwt-01/

- https://brownbears.tistory.com/440

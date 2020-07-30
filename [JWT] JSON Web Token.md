### 들어가기 전에, **Authentication(인증)** 과 **Authorization(권한)** 의 차이는?
 공통점 : 둘 다 __Connection Attempt__ 에 대한 일종의 확인이라는 점이다.  
- `Authentication(인증)`: 자신이 누구라고 주장하는 사람을 '누구구나~'하고 확인하는 절차 (ex. __로그인__)  
- `Authoriztion(권한)`: 특정 자원에 대한 __접근 권한__ 이 있는지 확인하는 절차. (ex. __로그인 이후 admin/not admin 구분__)

### 인증(Authentication)
인증(Authentication) 방법에는 __Session(Cookle)__ 와 __API Token__ 을 이용하는 방법이 있다.
- `서버기반` : Memory에 저장 (Session-Cookie-)
- `토큰기반` : Stateless. __인증정보를 서버에 담지 않음__ => 서버 확장이 쉽다. 
```
- API Token을 많이 사용하는 이유는, 위변조방지, DB조회 안해도 됨, 서버를 Stateless로 유지,  
서버확장성, Extensibility(다른 서비스에도 권한 공유 가능) 할 수 있기 때문이다.

- JWT는 API Token 중 보안성이 높은 라이브러리다. 
```

### 작동원리
1. __USER__ : Login  
2. **Server**: 계정정보 검증  
3. **Server**: User에게 `Signed`토큰 발급. 정상적으로 발급된 토큰임을 증명  
4. **Client**: 토큰 저장. 서버에 **Request** 할 때마다 Token을 서버에 전달 (Http Request의 Header에 토큰값 포함시켜 전달)  
5. **Server**: 토큰 검증, **Response**  

# 1. `Json Web Token` (JWT)
토큰 기반의 인증이 많아지면서, `Http Authorization`, `URI Query Parameter` 등 공백을 사용할 수 없는 곳에서  
가볍게 사용할 수 있는 토큰으로 설계되었다. 

# 2. JWT의 구조
- **JWT** = API 인증 등에 사용하는 Token  
- **JWT Token** = `.` 마침표를 기준으로 **3 부분**으로 나눠짐

### 1. `JOSE 헤더` : Json Object Signing & Encryption 
JWT Token을 어떻게 해석해야 하는지 명시한다.  
헤더를 열어보고 `JWT Claim Set`, `Signature`를 어떻게 해석할지 알 수 있음.  
  
```
new Buffer('{"alg":"HS256","typ":"JWT"}').toString("base64")
// eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
```
  
- 헤더 값이 {"alg":"HS256","typ":"JWT"} 라고 한다면, 이를 base64로 인코딩해서 JOSE 헤더를 생성한다.   
예제에서는 JWT 타입, HS256(HMAC) 알고리즘을 사용했다는 의미  

### 2. `JWT Claim Set` 
- **Token의 Body**로 토큰에 포함할 내용을 넣는다.  
- 토큰을 디코딩해서 바로 값을 확인할 수 있는 구조 - DB조회 없이 사용자 ID 등을 담아서 바로 사용할 수 있음.  



```
new Buffer('{"iss":"John Doe","exp":1434290400000,"username":"john","age":25,"iat":1434286842654}').toString("base64")
// eyJpc3MiOiJKb2huIERvZSIsImV4cCI6MTQzNDI5MDQwMDAwMCwidXNlcm5hbWUiOiJqb2huIiwiYWdlIjoyNSwiaWF0IjoxNDM0Mjg2ODQyNjU0fQ==
```
- 위 예제처럼 `base64`로 인코딩한다.   
마지막에 붙은 `==`은 padding 인데 여기서는 padding을 제거하고 토큰으로 사용한다.  


### 3. `Signature`
- **JOSE헤더**와 **JWT Claim Set**은 단순히 JSON 문자열을 base64로 인코딩한 것이다.  
즉, 누구나 이 값을 디코딩하면 JSON에 어떤 내용이 들어있는지 알 수 있기 때문에  
JOSE헤더, JWT Claim Set이 위변조되었는지 검증하기 위한 Signature 부분이 필요하다.   


##### References: 
- [JWT에 대해서 by Outsider](https://blog.outsider.ne.kr/1160)
- [JSON Web token 소개 및 구조 by Velopert](https://velopert.com/2389)
- [Auth0 JWT 공식 웹사이트](https://jwt.io/)
- [박하늬의 개발블로그](https://hanee24.github.io/2018/04/21/authentication-authorization/)

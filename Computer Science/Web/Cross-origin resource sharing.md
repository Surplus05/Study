# CORS

브라우저는 자원의 출처를 검증한다.

이 때, 기본적으로 허용 가능한 출처는 Same Origin만 가능하다.

- Same Origin  
  Protocol, Host, Port가 동일한 것.

그러나 Same Origin 이 아니더라도 CORS 정책만 따른다면 허용 해 준다.  
자원 요청시, 요청 헤더에 Origin 필드에 Origin 을 담아 보낸다.  
서버 응답시 Access-Control-Allow-Origin와 함께 이 Origin을 다시 보내주는데, 브라우저는 이 두 항목을 비교해 CORS 정책 위반 여부를 확인한다.  
Access-Control-Allow-Origin 에 보내준 Origin 이 포함된다면 정상적으로 받아오는 것이다.

그래서 CORS 에러의 가장 쉬운 해결법은 Access-Control-Allow-Origin 를 변경해 주는 것이다.

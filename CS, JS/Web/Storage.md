# storage  

브라우저에서 사용하는 저장소에는 Cookie, localStorage, sessionStorage 세 종류가 있다.  

## Cookie  
쿠키를 설정하면 모든 요청 헤더에 쿠키가 포함되게 된다.  
네트워크 응답 헤더의 Set-Cookie 속성을 사용해 만들 수도 있고, document.cookie 통해 프론트엔드 단에서 직접 설정해줄 수도 있다.  

용량제한이 작다. (4KB)  

다른 Site로 전송되는 쿠키를 서드 파티 쿠키라 하는데, 민감한 정보를 서드 파티 쿠키에 저장하면 문제가 발생할 수 있으니 유의하자.  
SameSite속성을 통해 None, Lax, Strict를 지정해 줄 수 있다.  
이 속성 뿐만 아니라 domain, path 등등 속성이 존재하니 자세한건 레퍼런스를 참고하자.  

Same-Site로 전송되는 쿠키를 퍼스트 파티 쿠키라 한다.  

참고 
* Cross-Domain은 Protocol, Domain, Port가 같아야 한다.  
* Same-Site는 Domain이 같아야 한다. Sub-Domain에 해당하는 경우도 Same-Site로 취급한다.  


## WebStorage  
만료기간이 없으며 키-값 형태로 저장된다.  
용량제한이 비교적 크다.  
localStorage 는 브라우저를 닫아도 계속 저장되며, sessionStorage는 브라우저를 닫으면 삭제된다.  



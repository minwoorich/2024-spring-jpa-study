## HTTP API 설계 예시
 - HTTP API - 컬렉션
    - POST 기반 등록
 - HTTP API - 스토어
    - PUT 기반 등록 
 - HTML FORM 사용
    - GET, POST만 지원 
    
    
## 회원 관리 시스템 
API설계 - POST 기반 등록 (컬렉션 /members)
![](https://velog.velcdn.com/images/pmmh9395/post/69299042-9958-4df0-81f0-bdc4b4f4ee66/image.png)
> 김영한쌤 "URI는 리소스를 식별해야지 리소스가 아닌 다른걸 식별하면 안돼요" 

![](https://velog.velcdn.com/images/pmmh9395/post/0d0181e6-fa13-43c1-b052-561519840c34/image.png)
> POST로 데이터 전송시 서버가 새로 등록된 리소스 URI를 생성해준다!!


## 파일 관리 시스템 
API설계 - PUT 기반 등록 (스토어 /files)
![](https://velog.velcdn.com/images/pmmh9395/post/29d1e076-194a-4360-9c7b-e1960eac51d5/image.png)
> POST와 가장큰 차이점 PUT은 URI를 클라이언트가 직접 리소스의 URI 관리를 한다.

![](https://velog.velcdn.com/images/pmmh9395/post/ad6a059e-6c81-4710-aa0e-e55743ced63e/image.png)

## HTML FORM 사용
- HTML FORM은 GET, POST만 지원
- AJAX 같은 기술을 사용해서 해결한다.
- 여기서는 순수 HTML, HTML FORM
- GET, POST만 지원하므로 제약이 있다.
![](https://velog.velcdn.com/images/pmmh9395/post/61269f41-3e95-43ee-b53b-64c0e4dd4e39/image.png)

![](https://velog.velcdn.com/images/pmmh9395/post/f2cbb136-8858-4e5a-a930-4a3e33c9b58f/image.png)

>이상적으로 HTTP 메서드로 해결하면 좋겠지만 불가능하다 . 
그래서 실무에서는 컨트롤 URI를 많이 사용한다.


### 참고하면 좋은 URI 설계 개념
![](https://velog.velcdn.com/images/pmmh9395/post/5fc89924-543f-4fe8-b84d-67de480e213c/image.png)


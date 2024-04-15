---
Http 메서드 활용에 대해 공부해보자.

# Http메서드 활용 
2가지에 대하여 정리한다.
>- 클라이언트에서 서버로 어떤식으로 데이터 전송하는지
- HTTP API 설계 예시 


### 클라이언트에서 서버로 데이터 전송
데이터 전달 방식은 크게 2가지이다.

1. **쿼리 파라미터를 통한 데이터 전송**
  	- GET
    - 주로 정렬 필터(검색어)

2. **메시지 바디를 통한 데이터 전송**
	- POST, PUT, PATCH
    - 회원 가입, 상품 주문, 리소스 등록, 리소스 변경 
    
    
### 클라이언트에서 서버로 데이터 전송
4가지 상황이 존재한다.
- 정적 데이터 조회
	
    - 이미지, 정적 텍스트문서
- 동적 데이터 조회
	
    - 주로 검색, 게시판 목록에서 정렬 필터(검색어)
- HTML Form을 통한 데이터 전송
	
    - 회원 가입, 상품 주문, 데이터 변경
- HTTP API를 통한 데이터 전송
	
    - 회원 가입, 상품 주문, 데이터 변경
    - 서버 to 서버, 앱 클라이언트, 웹 클라이언트(Ajax)
    
---
### 정적 데이터 조회
![](https://velog.velcdn.com/images/pmmh9395/post/48d70eeb-6535-4de1-aa9d-28ca3f999ed0/image.png)
> - 이미지, 정적 텍스트 문서
- 조회는 GET 사용
- 정적 데이터는 일반적으로 쿼리 파라미터 없이 리소스 경로로 단순하게 조회 가능


### 동적 데이터 조회
![](https://velog.velcdn.com/images/pmmh9395/post/8194124c-d458-4005-bad6-5b40ec39fbd8/image.png)
>- 주로 검색 게시판 목록에서 정렬 필터
- 조회 조건을 줄여주는 필터, 조회 결과를 정렬하는 정렬 조건에 주로 사용
- 조회는 GET 사용
- GET은 쿼리 파라미터 사용해서 데이터를 전달

### HTML Form을 통한 데이터 전송
POST 전송 - 저장
![](https://velog.velcdn.com/images/pmmh9395/post/d41e922c-7d49-42e7-8555-2fc912e76849/image.png)

GET 전송 - 저장 (리소스 변경이 있는곳에 GET 사용 X)
![](https://velog.velcdn.com/images/pmmh9395/post/2500a5c1-67b6-45ba-865a-62b03bf86630/image.png)

GET 전송 - 조회
![](https://velog.velcdn.com/images/pmmh9395/post/8fc8a23a-7c87-4d12-a9b1-5c14116e029a/image.png)

**multipart/form-data**
주로 바이너리 데이터를 전송할때 사용
![](https://velog.velcdn.com/images/pmmh9395/post/a6819a0c-fa18-4cb9-8e34-049896e684dd/image.png)

### HTML FORM 데이터 전송 정리
![](https://velog.velcdn.com/images/pmmh9395/post/4f39899e-1740-45f4-9da2-c3e464fa2a2a/image.png)

---
## HTTP API 데이터 전송
클라이언트에서 서버로 데이터를 바로 전송하는 것.
![](https://velog.velcdn.com/images/pmmh9395/post/41ed772e-bda1-4683-9819-6d9773912fba/image.png)
![](https://velog.velcdn.com/images/pmmh9395/post/f953bab3-a41a-419f-bdc2-a091507d8407/image.png)

HTTP설계는 2편에서....

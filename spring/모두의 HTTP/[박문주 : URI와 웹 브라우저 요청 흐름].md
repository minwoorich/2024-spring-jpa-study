>[김영한님의 스프링 로드맵](https://www.inflearn.com/roadmaps/373)을 수강하며 공부한 내용을 개인적으로 정리한 글입니다.

- **핵심 키워드** 
: `URI`, `URL`, `URN`, `Packet`,`Request`,`Response`

# Section 2. URI와 웹 브라우저 요청 흐름
## [ URI, URL, URN 용어 정리 ]
- #### URI (Uniform Resource Identifier)  
: 리소스를 식별하는 통합된 방법.  
인터넷에 존재하는 각종 정보들의 유일한 이름이나 위치를 표시하는 식별자.  
URL과 URN을 포괄하는 개념이다.  
  - 리소스란? 
    : URI로 식별할 수 있는 모든 것. HTML파일, 실시간 교통정보 등 
- #### URL (Uniform Resource Locator) 
: 네트워크 상 해당 리소스의 위치를 나타낸다.
- #### URN (Uniform Resource Name)  
: 이름으로 리소스를 식별한다.   
URL과 달리 리소스의 물리적 위치와 관계없이 리소스를 가리킨다.    
위치는 변할 수 있지만, 이름은 변하지 않는다.   
리소스의 물리적 위치가 변하더라도 URN을 가지고 있으면 리소스에 접근할 수 있다는 개념.  
ex. 도서ISBN (urn:isbn9788972441342)

- #### URL과 URI의 비교 
```
 
URL :  foo://example.com:8042/over/there?name=ferret#nose  
       \_/   \______________/\_________/ \_________/ \__/
        |           |            |            |        |
     scheme     authority       path        query   fragment
        |   _____________________|__
       / \ /                        \
URN :  urn:example:animal:ferret:nose
     
```
- #### URL ? URN?  
  : URN만으로 리소스를 찾을 수 있는 방법이 보편화되어 있지 않으므로,
   현실상 URL가 URI와 같은 의미로 쓰이는 경우가 많다.
  
## [URL의 구조]
```
[[URL의 구조]]   
scheme://[userinfo@]host[:port][/path][?query][#fragment]
```
구성요소 : `scheme`,`userinfo`,`host`,`port`,`path`,`query`,`framgment`
![](https://velog.velcdn.com/images/melodie104/post/599d20f0-112a-4fca-8a1e-69f28081c564/image.png)

- **scheme** : 사용하는 프로토콜을 명시. ex) HTTP, HTTPS(HttpSecure), FTP
  - protocol : 자원에 접근하는 방식에 대한 규칙
- **userinfo** : URL에 사용자 정보를 포함해서 인증. (거의 사용하지 않는다.)
- **host** : 호스트명. ex) www.google.com
  - 도메인명 또는 IP주소를 직접 사용 가능.
  - **host vs. domain ?**  
  : 
  
- **port** : 접속 포트를 의미. 생략 가능(http-80, https-443)
  - 왜 톰캣은 주로 8080 포트를 이용할까?  
  : 웹 어플리케이션 개발시 보통, 앞에 웹서버를 80포트로 띄우고, 그 뒤에 WAS를 8080포트로 띄우는 게 관례이다.  
  https://www.inflearn.com/questions/130303/%EC%9B%B9-%EC%84%9C%EB%B2%84-%ED%8F%AC%ED%8A%B8%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C
- **path** : 서버 내의 계층적 리소스 경로(ex. 디렉토리/파일)
- **query** (queryParam, queryString) : key=value형태의 문자열 쌍. 웹 서버에 제공하는 파라미터. ?로 시작 &로 구분
- fragment : 서버에 전송하는 정보는 아님, html 내부 북마크 용도
![](https://velog.velcdn.com/images/melodie104/post/abda30d2-68a0-41ba-a44f-60abae26dbee/image.jpg)




  
## [ 웹 브라우저 요청 흐름 ]
- #### URL을 웹브라우저에 입력하면 어떻게 처리되는가?

![](https://velog.velcdn.com/images/melodie104/post/7b931a44-c687-4fd3-b082-1c709e001f4b/image.png)


  > **application level (Application layer)**  
  **1.** 브라우저가 URL을 분석해 HTTP 요청 메시지 생성     
  **2.** 요청 메시지를 보낼 IP주소를 구한다. (아래 순서로 진행)     
  &nbsp;&nbsp; a. hosts 파일을 검색(보통 loopback만 있음. hosts파일 변조 - 보안과 관련)  
  &nbsp;&nbsp; b. DNS 캐시 (RAM에 저장된)  
  &nbsp;&nbsp; c. DNS 서버에 Query (캐시 서버가 콘텐츠 서버에 질의)  
  **3.** Socket 라이브러리를 통한 I/O     
  &nbsp;&nbsp;a. TCP/IP 연결(3way handshake : syn, syn-ack, ack)   
    &nbsp;&nbsp;b. 데이터 전달    
    
  >**OS level (Transport layer, Network layer)**  
  **6.** TCP/IP 패킷 생성 (송/수신지 IP주소, 송/수신지 Port번호, http메시지 등)    

  
 >**Network Interface (Network Access layer) **  
 **7.** LAN 드라이버, LAN장비를 통해 전송  
 
 > _요청메시지를 받은 후, 서버에서도 같은 방식으로 응답 메시지를 보내게 된다._

--------------------------
[그림 출처]  
URL 구조 1 : https://www.beusable.net/blog/?p=4507  
URL 구조 2 : (도서) TCP/IP 쉽게, 더 쉽게  

[hostname, subdomain, domain 차이]  
용어 정리가 잘된 블로그 : https://etloveguitar.tistory.com/112  

[웹 브라우저가 메시지를 만들어 서버에 보내는 과정]  
https://joanne.tistory.com/227  
https://velog.io/@bini/네트워크-한-번에-끝내는-DNS  
https://www.wisewiredbooks.com/csbooks/ch3-network-internet/tcp-ip-intro.html  

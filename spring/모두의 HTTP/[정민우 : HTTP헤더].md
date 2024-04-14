## 1. HTTP 헤더 개요
### 용도
 >헤더에는 HTTP 전송에 필요한 모든 부가정보들이 들어가며 _메시지 바디 내용, 메시지 바디의 크기, 압축, 인증, 서버정보 등등_ 정말 수 많은 헤더들이 존재한다.

<img src="https://velog.velcdn.com/images/minwoorich/post/259b2d91-7d10-4050-8abd-4df7d20fd1dd/image.png" style="margin:0;"/>

개발자도구를 키고 "Newtork" 탭을 누른 뒤 "Headers" 탭에 들어가면 확인 할 수 있다.

### HTTP 헤더 분류 : 과거와 현재

#### 과거 (RFC2616)
- **General 헤더** :  메시지 전체에 적용되는 정보
 ex) Connection : close

- **Request 헤더**  : 요청 정보 
ex) User-Agent : Mozilla/5.0

- **Response 헤더** : 응답 정보 
ex) Server : Apache
- **Entity 헤더** : 엔티티 바디 정보 
ex) Content-Type : text/html


#### 현재 (RFC723X)
과거에는 MessageBody 의 정보를 지닌 헤더를 따로 Entity 헤더로 구분하여 분류하였는데 HTTP 가 업그레이드돼고 스펙이 변경되면서 Entity 헤더는 사라지게 되었고 대신 대신 "표현" 이라는 개념이 등장하였다. 
(물론 실제로 헤더들이 사라진건 아니고 그냥 분류 체계가 개편되었다는게 좀 더 정확한 설명일것같다.)

<img src="https://velog.velcdn.com/images/minwoorich/post/c2807e47-1aff-4ed4-b889-d915edc72772/image.png" style="margin:0;width:600px"/>


과거 ``http/0.9`` 시절에는 클라이언트가 서버와 컨텐츠 협상이라는 기능을 지원 하지 않았다. "컨텐츠 협상" 이란, 뒤에 다루지만 간략히 설명하자면 클라이언트가 서버에게 그냥 리소스를 달라고 하는것이 아닌 자신이 원하는 타입으로 리소스를 달라고 요청 할 수 있는 기능이다.

먼 ~ 과거 인터넷이 그렇게 발달하지 않았을 시절에는 그냥 데이터가 전송만 되는것에 만족하며 살았을것이다. 하지만, 인간의 욕심은 끝이 없기에 지속해서 기술은 발달이 되었고 오늘날에 이르러서는 리소스들이 다양한 포맷으로 (텍스트, html, json, xml 등) 표현되기 시작하였다. 게다가 포맷 뿐만 아니라 언어나 인코딩 타입들도 다양해지면서 단순히 "리소스" 를 전송 하는 것을 넘어 , 그 리소스가 어떤 식으로 "표현" 되어 전송 할 것인지가 더 중요해졌다.

그래서 ``http/1.1`` 로 버전업이 되면서 "표현" 이라는 개념이 도입되었다. 그래서 이제 클라이언트들은 다양한 방식으로 "표현" 된 리소스들을 요청 할 수 있게 된것이다. 하지만 헤더들의 분류체계가 조금 old 한 것이 문제였다. 그래서 RFC723X 부터는 "Entity" 라는 헤더 분류를 없애고 아예 "Representation" 으로 새롭게 헤더를 분류하게 되었다. 

위 비교사진을 보다시피 새로운 RFC 스펙을 보면 이전보다 훨씬 더 세분화해서 분류해놓은것을 확인 할 수 있다.


- 엔티티(Entity) -> 표현 (Representation)
- Representation = representation Metadata + Representation Data
- 표현 = 표현 메타데이터 + 표현 데이터

#### 주요 표현 헤더들
- Content-Type : 표현 데이터의 형식
- Content-Encoding : 표현 데이터의 압축 방식
- Content-Language : 표현 데이터의 자연 언어
- Content-Length : 표현 데이터의 길이

## 2. 콘텐츠 협상

> **컨텐츠 협상(Content Negotiation)** 은 클라이언트와 서버 간에 어떤 표현 형식이나 언어, 인코딩 등을 사용할지 협상하는 과정을 말하며 이 과정을 통해 클라이언트가 요청한 리소스의 특정 표현 형식을 서버에게 알리고, 서버는 그에 맞는 적절한 표현을 선택하여 제공할 수 있다. 

이 기능이 없던 시절엔 서버는 항상 기본값의 리소스 형식만을 저장하고 있었고 클라이언트는 이를 다시 자신이 원하는 타입으로 변환을 해야하는 작업을 했어야했다. 혹은, 타입별로 서버가 리소스를 따로 따로 전부 저장했어야했는데 예를 들어 ``GET/news/en``, ``GET/news/kr/utf-8``, ``GET/news/kr``  이런식으로 페이지별로 전부 다르게 저장을 하고 있었어야해서 캐싱 및 최적화에 대한 문제점이 발생했다. 

#### 주요 협상 헤더들
- Accept : 클라이언트가 선호하는 미디어 타입 전달
- Accept-Charset : 클라이언트가 선호하는 문자 인코딩
- Accept-Encoding : 클라이언트가 선호하는 압축 인코딩
- Accept-Language : 클라이언트가 선호하는 자연 언어

#### 협상과 우선순위

**Quality Values (q)**
임의로 우선순위를 정해놓을 수 있음
<img src="https://velog.velcdn.com/images/minwoorich/post/f9ac7380-13b7-4c74-80cf-d187e24df484/image.png" style="margin:0;"/>

**구체적인것이 우선**
이 경우 text/plain;format=flowed 가 가장 높은 우선순위로 배치된다.
<img src="https://velog.velcdn.com/images/minwoorich/post/92fc2756-097c-4e6a-86e2-89c810ff7761/image.png" style="margin:0;"/>

## 3. 전송 방식
<img src="https://velog.velcdn.com/images/minwoorich/post/ed9063b4-ed0b-4258-9c91-deee229a4e98/image.png" style="margin:0;width:600px"/>

그냥 일반적인 전송. ``content-length`` 만 제공.

![]()
<img src="https://velog.velcdn.com/images/minwoorich/post/a6b50b8c-55df-4a69-8b58-f24eb538e8eb/image.png" style="margin:0;width:600px"/>

- 압축되어있어서 용량을 줄일 수 있음.
- ``content-encoding`` 을 지정해줘야함. 그래야지 클라이언트가 어떤 방식으로 압축되었는지 알 수 있음

<img src="https://velog.velcdn.com/images/minwoorich/post/5b968692-4e39-4223-bca8-ae1bc4f314a1/image.png" style="margin:0;width:600px"/>

- 서버가 데이터를 분할해서 전송해줌. 
- 클라이언트는 모두가 도착할때까지 **기다릴 필요없이** 분할되었지만 먼저 작은 덩어리를 받을 수 있다.
- content-length 없음 (얼만큼 잘려서 올지 모르기때문)

<img src="https://velog.velcdn.com/images/minwoorich/post/9010c577-7dfc-485a-9666-1e7b8ace0543/image.png" style="margin:0;width:600px"/>

- 데이터의 일부만을 지정해서 받아볼 수 있음
- 만일 1000MB 짜리를 절반정도 다운로드 하다가 중간에 끊겼더라면, 범위 전송을 통해 처음부터 다시 받는게 아닌 끊긴지점부터 특정 범위까지 전송해달라고 요청 할 수 있음

## 4. 일반 정보

### From 헤더
> 유저 에이전트의 이메일 정보

- 일반적으로 잘 사용되지 않음
- 검색 엔진 같은 곳에서, 주로 사용
- 요청용 헤더

### Referer
> 이전 웹 페이지 주소

- 사용자가 구글에서 나무위키로 이동하게 되면 Referer 헤더에는 "구글" 의 웹사이트 URL 이 저장된다
- Referer 를 사용해서 **유입 경로를 분석** 할 수 있다
- 사실 referrer 가 옳은 철자인데 오타난 이후로 고치지 못해 그대로 사용중
- 요청용 헤더

### User-Agent
>유저 에이전트 애플리케이션 정보

- 웹 브라우저 정보, 클라이언트 어플리케이션 정보
- 어떤 종류의 브라우저에서 장애가 발생하는지 파악 할 수 있다

### Server
> 요청을 처리하는 Origin 서버의 소프트웨어 정보

 - 프록시가 아닌 실제로 나의 요청을 처리해주는 원본 서버에 대한 정보
 - 응답 헤더에서 사용 (과거에는 요청에서도 사용했었음)
 
## 5. 특별한 정보
 
 ### Host
 > 요청한 호스트 정보(도메인)
 
  <img src="https://velog.velcdn.com/images/minwoorich/post/37762fd8-ecac-4ca5-8316-5d0b4db8fc7d/image.png" style="margin:0;"/>
  
 - 하나의 IP 주소에 여러 도메인이 적용되어 있을때 사용
 
 예를 들어, 200.200.200.2 이라는 IP 주소를 할당 받은 서버 하나에 여러 개의 어플리케이션이 가상 호스팅을 사용해 각자의 도메인을 가지고 구동 될 수 있음. 이 경우, 클라이언트는 이 Host 정보로 구분하여 원하는 어플리케이션과 연결을 할 수 있다.

#### 실제 Host 정보를 등록하는 화면 예시 (가비아)
<img src="https://velog.velcdn.com/images/minwoorich/post/0f18480e-846b-42ec-af85-b228900d4618/image.png" style="margin:0;"/>

### Location
>페이지 리다이렉션

- 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있다면 그 위치로 자동 이동해준다.

### Allow
> 허용 가능한 HTTP 메서드

서버 API 가

``GET /hello``
``POST /hello``
``PUT /hello``

이 3가지 URI 를 지원할 때, 만일 클라이언트가 ``PATCH /hello`` 로 요청 하게 될 경우 405 (Method Not Allowed) 에러를 반환한다.

### Retry-After
> 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간

- 503 에러가 발생한경우 서버는 Retry-After 헤더를 통해 서비스가 언제까지 불능인지 알려줄 수 있다. 

## 6. 인증

### Authorization
>클라이언트 인증 정보를 서버에 전달

- Authorization : 다양한 인증 방식이 존재하며 value에는 인증 방식에 따라 다양한 정보 값들이 저장될 수 있음. 

### WWW-Authenticate
> 리소스 접근시 필요한 인증 방법 정의

- 리소스 접근시 필요한 인증 방법 정의
- 401 Unauthorized 응답과 함게 사용

## 7. 쿠키

- 서버는 기본적으로 stateless 하게 동작함. 즉, 유저에 대한 정보를 저장하지 않음

- 그렇기 때문에 이론적으론 클라이언트가 로그인을 했더라도 다른페이지로 넘어갈때 서버는 해당 유저가 "홍길동" 인지 "김길동" 인지 "박길동" 인지 알 수 없음

- 즉, 요청 할 때마다 내가 "홍길동" 이라는 사용자 정보를 첨부해서 보내야하는데 이걸 매번 개발자가 담아서 보내는게 매우 번거로움.(ex. GET /login?user=홍길동)

- 그래서 "쿠키" 라는 개념을 도입함
- 서버는 클라이언트에게 응답을 보낼때 특정 유저정보들을 쿠키에 담아서 응답함
- 그러면 브라우저가 자동으로 본인의 쿠키 저장소에 사용자 정보를 저장해놓고 있다가, 서버로 요청 보낼때마다 자동으로 쿠키값도 같이 보내줌.

<img src="https://velog.velcdn.com/images/minwoorich/post/733bce5e-612d-42cc-9a0c-60313e752330/image.png" style="margin:0;width:600px"/>

<img src="https://velog.velcdn.com/images/minwoorich/post/791bcf4f-4d54-41be-ba57-d02ad4eaa403/image.png" style="margin:0;width:600px"/>

### 사용처
- 사용자 로그인 세션 관리 (MVC2 편에 자세하게 다룸)
- 광고 정보 트래킹

### 쿠키의 생명주기

- 만료일이 되면 쿠키를 삭제하게된다
- 세션 쿠키 : 만료 날짜를 생략하면 브라우저 종료시 까지만 유지
- 영속 쿠키  :만료 날짜를 입력하면 해당 날짜까지 유지


### 주의
보안에 민감한 데이터(주민번호, 신용카드 번호, 비밀번호,,,) 들은 저장하면 안 됨

>본 포스트는
[김영한의 모든 개발자를 위한 HTTP 웹 기본 지식 강의](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard) 를 보고 정리했습니다.

## 1. 캐시 기본 동작

### 캐시가 없을 때
- 데이터가 변경되지 않아도 계속 네트워크를 통해서 데이터를 다운로드 받아야함
- 느린 사용자 경험

예를 들어, 네이버에 "자동차 사진" 을 검색할 경우 수 많은 이미지들이 응답되어지는데 만일 여러번 검색할 경우 계속해서 똑같은 이미지 파일들을 멀리 떨어진 서버로부터 전달 받는 것이다. 


### 캐시 적용

<img src="https://velog.velcdn.com/images/minwoorich/post/04fc4940-8609-42ff-a4d6-6aaa3cbc44b3/image.png" style="margin:0;"/>

<img src="https://velog.velcdn.com/images/minwoorich/post/44a10f20-b5e9-4dca-82ff-c916e3deca13/image.png" style="margin:0;"/>

- 브라우저 캐시를 사용한다면, 같은 이미지를 계속해서 서버로부터 다운받는게 아니라 그냥 자신의 로컬 환경에 있는 이미지를 바로 꺼내 쓸 수 있는것이다.
- 네트워크를 사용하지 않음
- 빠른 사용자 경험

> 그럼 캐시 시간을 초과하면? 당연히 다시 서버를 통해 데이터를 다시 조회 하게 되고, 캐시를 갱신한다. 물론, 이때 다시 네트워크 다운로드가 발생하게된다.
> 
>이 문제를 어떻게 해결 할 수 있을까?

## 2. 검증 헤더와 조건부 요청 - 1

### Last-Modified, If-Modified-Since 헤더

<img src="https://velog.velcdn.com/images/minwoorich/post/cd5ec89f-b7ae-402f-a8f7-1034bc5435b9/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/655d77cc-22cc-4122-85ed-a47e7b7d4b43/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/deacd26a-3bd7-4e84-8a95-f707cfb8413e/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/cc04636a-4e0e-45c8-9cf4-c3cf7267e2c3/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/1a4f6c23-f764-4698-8ab1-52a13cf50b4c/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/0d9d1eda-502a-46ad-97b5-73184bfffb9c/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/eea396fd-0c97-4b96-bf5d-3569b409ba32/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/f7e26f55-b355-4fcb-9898-2ab5c9c3f8bf/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/7d49490c-d6bf-490d-98d0-51deede2e101/image.png" style="margin:0;width:700px"/>

캐시 유효시간이 초과 했을지라도 서버의 데이터가 갱신되지 않았더라면, 즉, 클라이언트의 캐시에 저장된 것과 동일한 버전이라면 서버는 "그냥 너 캐시에 있는거 써도 돼~" 라는 메시지 (304 Not Modified) 를 전달한다. 이때 당연히 메시지바디는 없다.

클라이언트는 캐시에 저장되어있는 데이터를 재활용 할 수 있게되는것이다.

물론 네트워크를 사용하나 메시지 바디가 없기 때문에 매우 작은 헤더 용량만큼만 다운로드를 하게되므로 매우 실용적인 해결책이라 볼 수 있다.


<img src="https://velog.velcdn.com/images/minwoorich/post/0f69ec84-9a06-4cdd-a8d1-bfa21ee91c4a/image.png" style="margin:0;"/>

색깔이 연한 녀석들은 캐시에서 불러왔음을 나타낸다.

#### Last-Modified, If-Modified-Since 의 단점

- 만일 A라는 데이터에 아주 사소한 수정이 있는경우 (주석추가, 띄어쓰기 추가,,,)
- 수정을 하긴 했는데 A -> B -> A 로 다시 원상태로 복구하는 수정을 한 경우

위 두 상황에도 모두 수정이 된것이기 때문에 ``Last-Modified`` 날짜가 바뀌게된다. 즉, 클라이언트는 컨텐츠는 같을지라도 수정날짜가 다르기 때문에 다시 전체 파일을 다운로드 받아야한다.

> 이를 해결하는 좋은 방법 없을까?!

## 2. 검증 헤더와 조건부 요청 - 2
### Etag, If-None-Match 헤더

``Last-Modified`` 와 ``If-Modified-Since`` 헤더의 경우 파일의 상태가 아닌 날짜를 기준으로 검증을 했기 때문에 위와 같은 문제점이 발생한것이다. 반면 ``Etag``, ``If-None-Match`` 는 날짜가 아닌 파일의 변경 상태를 기준으로 검증을 하기 때문에 설령 수정이 발생했더라도 만일 변경점이 없다면 (해시 함수 사용) 그대로 캐시된 데이터를 사용 할 수 있도록 한다.

<img src="https://velog.velcdn.com/images/minwoorich/post/ce84f196-b920-4cf1-9fd1-cc2d0addf36a/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/36678170-9c00-4dca-9d6a-8429d1c1988c/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/94593e00-aa65-40cf-8677-b3cb9d41ac63/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/16791f50-39bd-4a19-8056-e3129374f5f0/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/44dcf50d-6439-48ed-8a81-43acf52c0e83/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/51dd91be-0249-480e-a9ef-922eb22935b3/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/41f971ca-bece-4af6-b3d1-dbd2d88f5135/image.png" style="margin:0;width:700px"/>
<img src="https://velog.velcdn.com/images/minwoorich/post/5031fb8a-604e-4169-a6ad-74ef278462ca/image.png" style="margin:0;width:700px"/>


- **캐시 제어 로직을 서버에서 완전히 관리** 할 수 있게 되었다.
- 클라이언트는 서버의 캐싱 메커니즘을 몰라도 되며 오직 ETag 만 서버에 보내서 같으면 유지, 다르면 다시 받는것이다.

## 3. 캐시와 조건부 요청 헤더

### Cache-Control
#### max-age
: 캐시 유효 시간, 초 단위
#### no-cache 
: 데이터는 캐시해도 되지만, 항상 origin 서버에 검증하고 사용. (프록시말고 원본서버를 통해 검증 하기를 원하는 설정)
#### no-store 
: 캐싱 안함 설정. 민감한 데이터의 경우 캐싱하면 안 되므로.

## 4. 프록시 캐시
>**프록시 캐시 서버(Proxy Cache Server)** 는 클라이언트와 원격 서버 간의 중간에 위치한 서버로, 클라이언트의 요청을 대신 받아들여 해당 요청에 대한 응답을 캐싱하여 저장하는 역할을 한다. 

- 자주 사용자들로 부터 조회되는 컨텐츠들은 멀리 있는 origin 서버가 아닌 프록시 서버를 두어 그곳에 저장을 해놓는다. 
- 그래서 유튜브 같은데서 인기 없는 영상의 경우 영상 뜨는데 꽤 오래 걸리는 경우가 종종 있음.(origin 으로 부터 다운로드 받는 중임)

<img src="https://velog.velcdn.com/images/minwoorich/post/49f09c7d-8451-40d0-83b2-e8b6b96f1e15/image.png" style="margin:0;"/>

## 5. 캐시 무효화

간혹 Cache-Control 에 no-store 를 사용했더라도 브라우저가 자기 멋대로 임의로 캐시를 하는 경우가 종종 있다. 그래서 정말 보안에 민감한 데이터일 경우 아래와 같이 모든 설정들을 전부 넣어줘야한다. 

#### Cache-Control : no-cache, no-store, must-revalidate

- **must-revalidate** 
	
    - 반드시 origin 서버에서 검증을 받아야 한다
    - origin 서버에 접근이 실패 할 경우 반드시 오류가 발생해야한다
    - 그냥 ``no-cache`` 만 설정 할 경우, 프록시 캐시서버에서 임의로 예전 캐싱했던 오래된 데이터를 보내주는 경우도 있는데 매우 민감한 정보라면 이것도 위험하므로 ``must-revalidate`` 를 사용한다. 
#### Pragma : no-cache (``http/1.0`` 하위 호환을 위해)



>본 포스트는
[김영한의 모든 개발자를 위한 HTTP 웹 기본 지식 강의](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard) 를 보고 정리했습니다.

# api 명세
모든 요청의 URI는 /api/ 로 시작한다.<br>
***굵은 이탈릭체*** 로 쓰여진 api는 Authorization 헤더를 필요로 하지 않는다.
그 외의 모든 api는 Authorization 헤더에 JWT 토큰을 포함해야한다.

## 요청 본문 & 응답에 포함되는 json 객체들
### Goal 
```JSON
{
"goalId": 1,
"memberEmail": "example@e.com",
"category": "testCategory",
"goalName": "testGoalName",
"content": "testGoalContent",
"limitDate": "2022-03-27T10:45:04.847+00:00",
"money": 10000,
"reward": "high",
"verificationResult": "success"
}
```

#### 각 항목별 설명
1. goalId 목표의 식별번호이다. 1 이상의 양의 정수다. 생성 요청시에는 포함되지 않는다.
2. memberEmail 은 회원의 email 주소다. 중복되지 않는다. 타인의 목표조회시 "example@e.com"이 적혀있다.
3. category 는 목표의 카테고리다. GET /goals/categories 의 응답에 포함된 값만 값으로 가질 수 있다.
4. goalName은 목표의 이름이다. 별도의 제한이 없다.
5. content는 목표의 본문이다. 별도의 제한이 없다. 
6. limitDate 는 목표의 제한시간이다.
7. money는 목표에 걸린 보증금의 액수이다. 
8. reward 는 목표달성시 보상금 지급 방법을 의미한다. GET /goals/rewards 에 포함된 값만 값으로 가질 수 있다.
9. verificationResult 는 현재 목표의 상태를 말한다. success, fail, ongoing, hold 중 하나이다.

### Certification
```JSON
{
"certId": 1,
"goalId": 1,
"content": "본문",
"image": "이미지",
"requireSuccessCount": 10,
"successCount": 1,
"failCount": 1
}
```
#### 각 항목별 설명
1. certId는 인증의 식별번호이다. 1 이상의 양의 정수이다. 생성 요청시에는 포함되지 않는다.
2. goalId는 인증에 대응되는 목표의 식별번호이다. 
3. content 는 인증의 본문이다. 
4. image는 인증의 인증사진으로[ Data URIs ](https://developer.mozilla.org/ko/docs/Web/HTTP/Basics_of_HTTP/Data_URIs " Data URIs ")방식의 값이다.
5. requireSuccessCount 는 인증의 최종 성공검증을 위한 필요 성공검증 횟수다.
6. successCount는 현재 인증의 성공검증 횟수를 의미한다.
7. failCount는 현재 인증의 실패검증 횟수를 의미한다.

### Member
```JSON
{
"email": "example@e.com",
"password": "password",
"nickName": "nickname",
"sex": "FEMALE",
"age": 20,
"money": 0
}
```
#### 각 항목별 설명
1. email은 회원의 email이다. 중복되지 않는다.
2. password는 회원의 비밀번호이다. 응답데이터에는 포함되지 않는다.
3. nickName은 회원의 닉네임이다. 중복이 가능하다.
4. sex는 회원의 성별이다. FEMALE, MALE, UNKNOWN 중 하나이다.
5. age는 회원의 나이이다.
6. money는 회원의 충전금 잔액이다.
### Notification
```JSON
{
    "notificationId": 8,
    "content": "카테고리 : 목표검증;실패검증 횟수가 누적되어 인증이 보류되었습니다. 이의제기 하기 전 까지 판정이 보류됩니다.",
    "memberEmail": "cks@naver.com",
    "date": "2022-06-29T10:38:47.000+00:00",
    "url": "GET /api/members/myinfo",
    "read": true
}
```
#### 각 항목별 설명
1. notificationId는 알림의 고유 인덱스다.
2. content 는 알림의 카테고리와 알림 본문을 의미한다. ";" 를 기준으로 좌측에는 카테고리, 우측에는 알림 본문이 포함된다.
3. memberEmail 은 알림의 소유주의 이메일을 의미한다.
4. date는 알림 발생 시간을 의미한다.
5. url은 알림을 눌렀을 시 이동할 url을 의미한다.
6. read는 알림의 읽음여부를 의미한다.
### Error
각종 api에서 잘못된 요청이나 서버의 문제로 인해 에러를 반환할 때 다음과 같은 형태의 JSON을 반환한다. HTTP 상태코드는 각 api에서 다로 정의한다.
```JSON
{
    "errorCode": 7,
    "errorContent": "다이어트 category is not found",
    "url": "POST /api/goals",
    "dateTime": "2022-06-12 22:02:35"
}
```
#### 각 항목별 설명
1. errorCode 는 별도로 정의된 에러 코드이다. HTTP 상태코드와 무관하다. 각 에러코드의 의미는 다음과 같다

    |에러코드|설명|
    |---|---|
    |1|중복된 이메일. 회원가입시 중복된 이메일인 경우|
    |2|로그인 실패. 이메일이나 비밀번호가 알맞지 않은 경우.|
    |3|유효하지 않은 토큰. 토큰이 만료되었거나 손상됨|
    |4|이미 인증이 존재함. 이미 인증이 등록된 목표에 인증을 등록하려는 경우 발생함|
    |5|권한 없음. 각종 상황에서 권한이 없는 요청을 한 경우|
    |6|돈이 넘침. 충전금보다 많은 금액으로 목표를 등록하거나, 충전 한계금액 이상으로 충전하려는 경우|
    |7|유효하지 않은 카테고리. 목표등록및 조회시 존재하지 않은 카테고리일 경우 발생함.|
    |8|발견되지 않음. 각종 요청에서 응답으로 넘겨줄 데이터가 발견되지 않은 경우|
    |9|알수 없음. 어떤 이유로 발생했는지 알 수 없음.(알려줄 수 없음)|
2. errorContent 는 상세한 에러상황을 설명하는 텍스트다.
3. url 은 사용자가 보낸 요청 url이다.
4. dateTime 은 응답시간이다. yyyy-MM-dd kk:mm:ss 형태이다.

**예외적으로 정의되지 않은 URL로 요청을 하거나, 심각한 서버 오류(별도로 코드로 제어하지 못한 예외가 발생한 경우) 다음과 같은 형태로 에러가 발생한다.**
```JSON
{
    "timestamp": "2022-06-12T13:20:12.752+00:00",
    "status": 404,
    "error": "Not Found",
    "path": "/api/goalsd"
}
```

## 인증 키
회원 가입, 로그인, 이메일 중복확인등 몇개의 특별한 경우(/api/members","/api/members/login","/api/admin","/api/statistics/total","/api/admin/**","/api/members/check/email/*)를 제외한 모든 요청은 Authorization 헤더에 유효한 토큰을 포함해야 한다.<br>
인증키를 발급받기 위해서는 다음 api를 사용하면 된다.

### ***/members/login***
#### 지원 메서드
POST
#### body
```JSON
{
    "email" : "example@e.com",
    "password" : "비밀번호"
}
```
#### 응답
##### 성공시
http 상태코드 : 200
```JSON
{
    "Authorization" : "토큰"
}
```
토큰은 JWT이다. header.payload.sign 으로 구성되어있다. payload 부분을 base64 디코딩하면, 다음과 같은 형태의 json을 얻을 수 있다.
```JSON
{
    "exp":  20220407223060,
    "email": "example@example.com"
}
```
## 이미지 관련 api
### /api/image/cert/{goalId}
#### 지원 메서드
GET : 해당 인증의 이미지를 조회한다.
#### 응답 데이터 
##### 성공시
200 OK, 이미지
##### 실패시
1. 404 : 이미지가 없는경우(= 존재하지 않는 인증을 조회한 경우)
2. 500 : 서버에러로 이미지를 읽을 수 없는 경우

### /api/image/announcement/{announcementId}
#### 지원 메서드
GET : 해당 공지사항 본문 이미지를 조회한다.
#### 응답 데이터 
##### 성공시
200 OK, 이미지
##### 실패시
1. 404 : 이미지가 없는경우(= 없는 공지사항 번호를 조회한 경우)
2. 500 : 서버에러로 이미지를 읽을 수 없는 경우

### /api/image/announcement/banner/{announcementId}
#### 지원 메서드
GET : 해당 공지사항 배너 이미지를 조회한다.
#### 응답 데이터 
##### 성공시
200 OK, 이미지
##### 실패시
1. 404 : 이미지가 없는경우(= 없는 공지사항 번호를 조회한 경우)
2. 500 : 서버에러로 이미지를 읽을 수 없는 경우

## 목표 관련 api
### /goals
#### 지원 메서드 
POST
#### body
Goal 단, goalId는 포함되지 않는다. 포함되더라도 무시된다.
#### 응답 데이터
##### 성공시 

200 OK, body에 goalId가 포함된 Goal

##### 실패시
1. json 요청에 email이 자신의 이메일과 일치하지 않는 경우 : 401 UNAUTHORIZED
2. json 요청에 category가 존재하지 않는 카테고리일 경우 : 400 BAD_REQUEST
3. json 요청의 money가 사용자의 충전금보다 큰 경우 : 403 FORBIDDEN
### /goals/categories 
#### 지원 메서드
GET : 카테고리 목록 조회
#### 응답데이터
##### 성공시
200 OK, String 배열 형태의 카테고리 목록 
##### 실패시
500 INTERNAL_SERVER_ERROR
### /goals/{goalID}
{goalID}는 목표의 식별번호로 1이상의 양수
#### 지원 메서드
GET
#### 응답데이터
##### 성공시
200 OK, body에 goalId가 포함된 Goal
##### 실패시
goalId의 Goal이 없는 경우 : 404 NOT_FOUND
### /goals/{category}/list/{state}/{page}
1. {category}는 목표의 카테고리로 문자열이다.all일 경우 모든 카테고리를 
2. {state}는 목표의 달성 상태를 나타내는 문자열로 all,ongoing,success,fail,hold 중 하나이다.
3. {page}는 페이지다. 1 페이즈부터 존재한다. 
#### 지원 메서드 
GET
#### 응답데이터
##### 성공시
200 OK, body에 Goal의 배열. 최대 6개의 Goal 객체로 이루어짐. 
### /goals/cert/{goalId}
{goalID}는 목표의 식별번호로 1이상의 양수
#### 지원 메서드 및 의미
1. POST : 목표에 대응되는 인증 등록
2. GET : 목표에 대응되는 인증 조회
#### body
POST : Certification, 단, certId 미포함. 포함되어도 무시
#### 응답데이터
##### 성공시 
1. POST : 200 OK, 생성된 Certification이 body에 포함
2. GET : 200 OK, 해당되는 Certification이 body에 포함
##### 실패시
1. GET 
    - 해당되는 인증이 없는 경우 : 404 NOT_FOUND
2. POST
    - 자신이 등록한 목표가 아닌 인증을 등록 시도할 경우 : 401 UNAUTHORIZED
    - 이미 해당 목표의 인증이 존재하는 경우 : 409 CONFLICT
    - 인증이 등록되었으나 모종의 이유로 인증을 가져오지 못한 경우 : 201 CREATED
### /goals/cert/success/{goalID}
#### 지원 메서드
PUT : 목표에 대응되는 인증의 성공검증
#### 응답데이터
##### 성공시
200 OK
##### 실패시
1. 자신이 등록한 인증을 자신이 검증하려는 경우 : 401 UNAUTHORIZED
2. 그 외의 경우 : 500 INTERNAL_SERVER_ERROR
### /goals/cert/fail/{goalID}
#### 지원 메서드
PUT : 목표에 대응되는 인증의 실패검증
#### 응답데이터
##### 성공시
200 OK
##### 실패시
1. 자신이 등록한 인증을 자신이 검증하려는 경우 : 401 UNAUTHORIZED
2. 그 외의 경우 : 500 INTERNAL_SERVER_ERROR
## 통계관련 api

### ***/statistics/total***
#### 지원 메서드
GET : 전체 통계 조회
<br>인증 토큰 필요없음.
#### 응답 데이터 
```JSON
{
    "totalSuccessGoalCount": 1, // 등록된 목표 중 성공한 목표 개수
    "totalGoalCount": 4, // 전체 목표 개수
    "totalOngoingGoalCount": 2, // 현재 진행중인 목표 개수
    "totalFailGoalCount": 0 // 실패한 목표 개수
}
```

### /statistics/mine
#### 지원 메서드
GET : 자신이 등록한 목표에 대한 통계 조회
#### 응답 데이터 
```JSON
{
    "totalSuccessGoalCount": 1, // 등록된 목표 중 성공한 목표 개수
    "totalGoalCount": 4, // 전체 목표 개수
    "totalOngoingGoalCount": 2, // 현재 진행중인 목표 개수
    "totalFailGoalCount": 0 // 실패한 목표 개수
}
```
## 회원 관련 api

### ***/members***
#### 지원 메서드
POST
#### body
Member. 단, money는 포함되지 않음. 포함되도 무시함
#### 응답 데이터
##### 성공시
200 OK
##### 실패시
이미 가입된 이메일인경우 : 409 CONFLICT
### /members/check/email/{email}
#### 지원 메서드
{email} : 이메일
1. GET : 해당 이메일로 회원가입이 가능한지 확인
#### 응답데이터
1. true : 회원가입 가능
2. false : 회원가입 불가
### /members/myinfo
#### 지원 메서드
1. GET : 개인정보 조회
2. PUT : 개인정보 수정
#### body
PUT : Member. 수정하기 싫은 값은 기존 값을 그대로 전달해야 함. 단, 이메일은 무시됨.
#### 응답데이터
##### 성공시 
1. GET : 200 OK, Member가 body에 포함
2. PUT : 200 OK, 변경된 내용이 반영된 Member가 body에 포함
##### 실패시
1. GET 
    - 인증토큰이 유효하지 않거나, 로그인되지 않은 경우 : 401 UNAUTHORIZED
2. PUT
    - 타인의 정보를 변경시도한 경우 : 401 UNAUTHORIZED
    - 해당되는 Member가 없는 경우(심각한 무결성 오류상태) : 500 INTERNAL_SERVER_ERROR
### /members/myinfo/goals/{state}/{page}
1. {state}는 목표의 달성 상태를 나타내는 문자열로 all, ongoing, success, fail, hold, oncertification 중 하나이다.
2. {page}는 페이지다. 1 페이즈부터 존재한다. 
#### 지원 메서드
GET : 자신이 등록한 목표 목록 조회
#### 응답 데이터
##### 성공시
200 OK, 최대 6개의 Goal이 포함된 배열
##### 실패시
1. 로그인이 제대로 되지 않은 경우 : 401 UNAUTHORIZED
2. state가 잘못된 경우 : 400 BAD_REQUEST
### /members/myinfo/cert/{state}/{page}
1. {state}는 목표의 달성 상태를 나타내는 문자열로 all, ongoing, success, fail, hold, oncertification 중 하나이다.
2. {page}는 페이지다. 1 페이즈부터 존재한다. 페이지당 6개씩 불러온다.
#### 지원 메서드
GET : 자신이 등록한 인증 목록 조회
#### 응답 데이터
##### 성공시
200 OK, 최대페이지와 최대 6개의 Certification이 포함된 배열
##### 실패시
1. 로그인이 제대로 되지 않은 경우 : 401 UNAUTHORIZED
2. state가 잘못된 경우 : 400 BAD_REQUEST
### /members/myinfo/notifications
#### 지원 메서드
GET : 자신의 알림 목록을 조회
#### 응답 데이터
##### 성공시
200 OK, Notification의 배열
##### 실패시
1. 500 INTERNAL_SERVER_ERROR
### /members/myinfo/notifications/{notiId}
#### 지원 메서드
PUT : notiification_id가 notiId인 알림을 읽기
#### 응답 데이터
##### 성공시
200 OK
##### 실패시
1. 403 FORBIDDEN : 자신의 알림이 아닌 것을 읽기 시도한 경우
2. 404 NOTFOUND : 해당 알림이 없는 경우
### /members/myinfo/charge
#### 지원 메서드 
PUT : 충전
#### body
Member. email, password, money 필수 나머진 무시
#### 응답 데이터
##### 성공시
200 OK
##### 실패시
1. 비밀번호가 잘못된 경우 : 401 UNAUTHORIZED 
### /members/myinfo/refund
#### 지원 메서드 
PUT : 인출
#### body
Member. email, password, money 필수 나머진 무시
#### 응답 데이터
##### 성공시
200 OK
##### 실패시
1. 비밀번호가 잘못된 경우 : 401 UNAUTHORIZED 

## 관리자 api
### /admin/login
#### 지원 메서드
POST : 관리자 로그인
#### body
```JSON
{
    "email" : "admin@e.com",
    "password" : "password"
}
```
#### 응답
##### 성공시
http 상태코드 : 200
```JSON
{
    "Authorization" : "토큰"
}
```
토큰은 JWT이다. header.payload.sign 으로 구성되어있다. payload 부분을 base64 디코딩하면, 다음과 같은 형태의 json을 얻을 수 있다.
```JSON
{
    "exp":  20220407223060,
    "email": "example@example.com"
}
```
### /admin/goals/hold/{page}
page : 페이지
#### 지원 메서드 
GET : 보류중인 목표 및 인증 조회 한 페이지당 최대 9개의 객체가 포함됨.
#### 응답
200 OK
```JSON
[
    {
        "goal": {
            "goalId": 12,
            "memberEmail": "example@e.com",
            "category": "운동",
            "goalName": "testGoalName",
            "content": "testGoalContent",
            "limitDate": "2022-08-27T10:45:04.000+00:00",
            "money": 10000,
            "reward": "high",
            "verificationResult": "hold"
        },
        "certification": {
            "certId": 0,
            "goalId": 0,
            "content": null,
            "image": null,
            "requireSuccessCount": 0,
            "successCount": 0,
            "failCount": 0,
            "verificationResult": null
        }
    }
    ,
    {
        "goal": {
            "goalId": 13,
            "memberEmail": "example@e.com",
            "category": "운동",
            "goalName": "testGoalName",
            "content": "testGoalContent",
            "limitDate": "2022-08-28T10:45:04.000+00:00",
            "money": 10000,
            "reward": "high",
            "verificationResult": "hold"
        },
        "certification": {
            "certId": 0,
            "goalId": 0,
            "content": null,
            "image": null,
            "requireSuccessCount": 0,
            "successCount": 0,
            "failCount": 0,
            "verificationResult": null
        }
    }
]
```
### /admin/goals/cert/success/{goalId}
#### 지원 메서드
GET : goalId 에 해당하는 목표를 성공판정.
#### 응답 
200 OK
<br>
실패시 500 

### /admin/goals/cert/fail/{goalId}
#### 지원 메서드
GET : goalId 에 해당하는 목표를 성공판정.
#### 응답 
200 OK
<br>
실패시 500 

### /admin/announcement
#### 지원 메서드
POST : 공지사항 등록
#### body
```JSON
{
    "title":"dasdad", // 공지사항 제목
    "description":"dsda", //공지사항 간단 설명
    "image" : "data URI", // 공지사항의 본문에 해당하는 이미지의 Data URI
    "bannerImage" : "data URI", // 공지사항의 배너에 해당하는 이미지의 Data URI
    "activation":true // 공지사항의 활성여부를 결정하는 값
}
```
#### 응답
```JSON
{
    "announcementId": 1, // 공지사항 번호
    "title": "dasdad", // 제목
    "description": "dsda", // 설명
    "date": "2022-06-28T12:04:11.204+00:00", //작성 시간
    "activation": true //활성상태
}
```
## 기타 api
### /api/announcements/list/{page}
page : 페이지. 한 페이지당 6개씩 
#### 지원 메서드
GET 공지사항 목록 조회 
#### 응답 데이터
```JSON
{
    "maxPage": 1, // 최대 페이지
    "announcements": [
        {
            "announcementId": 1, // 공지사항 번호
            "title": "dasdad", // 제목
            "description": "dsda", // 설명
            "date": "2022-06-28T12:04:11.204+00:00", //작성 시간
            "activation": true //활성상태
        },
        {
            "announcementId": 2, // 공지사항 번호
            "title": "dasdad", // 제목
            "description": "dsda", // 설명
            "date": "2022-06-29T12:04:11.204+00:00", //작성 시간
            "activation": true //활성상태
        }
    ]
}
```

# api 명세
모든 요청의 URI는 /api/ 로 시작한다.

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

## 인증 키
회원 가입과 로그인을 제외한 모든 요청은 Authorization 헤더에 유효한 토큰을 포함해야 한다.<br>
인증키를 발급받기 위해서는 다음 api를 사용하면 된다.

### /members/login
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
1. {category}는 목표의 카테고리로 문자열이다.
2. {state}는 목표의 달성 상태를 나타내는 문자열로 all,ongoing,success,fail,hold 중 하나이다.
3. {page}는 페이지다. 1 페이즈부터 존재한다. 
#### 지원 메서드 
GET
#### 응답데이터
##### 성공시
200 OK, body에 Goal의 배열. 최대 9개의 Goal 객체로 이루어짐. 
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

### /statistics/{type}/{year}
{type} 은 어떤 통계를 의미하는지 표현함.
1. all : 전체 등록된 목표 갯수
2. success : 성공한 목표 갯수
3. fail : 실패한 목표 갯수
4. ongoing : 현재 진행중인 목표 개수
5. hold : 검증 보류중인 목표 개수

{year}는 통계대상 년도를 의미함. 4자리 숫자 
#### 지원 메서드
GET : 전체 통계 조회
#### 응답 데이터 
숫자

### /statistics/private/{type}/{year}
{type} 은 어떤 통계를 의미하는지 표현함.
1. all : 전체 등록된 목표 갯수
2. success : 성공한 목표 갯수
3. fail : 실패한 목표 갯수
4. ongoing : 현재 진행중인 목표 개수
5. hold : 검증 보류중인 목표 개수

{year}는 통계대상 년도를 의미함. 4자리 숫자 
#### 지원 메서드
GET : 자신이 등록한 목표에 대한 통계 조회
#### 응답 데이터 
숫자

## 회원 관련 api

### /members
#### 지원 메서드
POST
#### body
Member. 단, money는 포함되지 않음. 포함되도 무시함
#### 응답 데이터
##### 성공시
200 OK
##### 실패시
이미 가입된 이메일인경우 : 409 CONFLICT

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
### /myinfo/goals/{state}/{page}
1. {state}는 목표의 달성 상태를 나타내는 문자열로 all,ongoing,success,fail,hold 중 하나이다.
2. {page}는 페이지다. 1 페이즈부터 존재한다. 
#### 지원 메서드
GET : 자신이 등록한 목표 목록 조회
#### 응답 데이터
##### 성공시
200 OK, 최대 6개의 Goal이 포함된 배열
##### 실패시
1. 로그인이 제대로 되지 않은 경우 : 401 UNAUTHORIZED
2. state가 잘못된 경우 : 400 BAD_REQUEST
### /myinfo/notifications
#### 지원 메서드
GET : 자신의 알림 목록을 조회
#### 응답 데이터
##### 성공시
200 OK, 모든 알림 이 포함된 배열
##### 실패시
1. 500 INTERNAL_SERVER_ERROR
### /myinfo/charge
#### 지원 메서드 
PUT : 충전
#### body
Member. email, password, money 필수 나머진 무시
#### 응답 데이터
##### 성공시
200 OK
##### 실패시
1. 비밀번호가 잘못된 경우 : 401 UNAUTHORIZED 
### /myinfo/refund
#### 지원 메서드 
PUT : 인출
#### body
Member. email, password, money 필수 나머진 무시
#### 응답 데이터
##### 성공시
200 OK
##### 실패시
1. 비밀번호가 잘못된 경우 : 401 UNAUTHORIZED 
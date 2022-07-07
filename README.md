# 목표달성 시스템

## 기획의도 및 기초 컨셉
목표를 달성하기란 쉬운게 아닙니다. 목표달성을 위해선 강력한 계기가 필요합니다. 모두에게 강력한 계기가 될 수 있는 것은 돈입니다.<br> 
이 시스템은 목표와 함께 보증금을 등록하고 목표달성에 성공하면 보증금과 추가로 축하금을 줍니다. 반면, 달성에 실패하면 보증금을 가져갑니다. 

## 상세 기능
### 용어 정리
1. 인증 : 목표를 달성했다고 인증하는 게시글

2. 검증 : 인증이 진짜인지 검증하는 행위. 실패검증과 성공검증이 있음.
    - 실패검증 : 목표달성에 실패했음을 의미

    - 성공검증 : 목표달성에 성공했음을 의미

### 회원관리기능
1. 회원가입이 가능합니다. 회원가입 시 "이메일, 비밀번호, 닉네임, 성별, 연령대" 정보를 수집합니다.

2. 회원가입시 이메일로 인증메일이 발송되고, 인증이 완료되어야 가입이 확정됩니다.

3. 회원 가입 이외의 모든 기능은 로그인 이후 가능합니다.

### 충전및 이체 기능
1. 회원은 10,000원 단위로 충전이 가능합니다.

2. 회원은 모든 단위로 출금이 가능합니다.

### 일반 목표등록 기능
1. 회원은 목표를 등록할 때 "목표 이름, 목표 카테고리(정해져있는 항목에서 ), 상세 내용, 기간 제한, 보증금, 달성(실패)시 추가금 지급방식" 을 입력합니다.

2. 보증금은 최소 만원 부터 최대 100만원까지 만원단위로 설정 가능합니다. 단, 보증금은 계정에 충전되어있는 만큼만 설정할 수 있습니다.

3. 목표등록과 동시에 보증금이 차감됩니다.

3. 달성시 추가금 지급 방식은 리스크에 따라 2가지로 설정할 수 있습니다.(방식 이름은 가제입니다.)
    - 하이리스크 하이리턴 방식 : 실패시 보증금 전체를 잃고 성공시 보증금의 50%를 추가로 받습니다.

    - 로우리스크 로우리턴 방식 : 실패시 보증금의 50%를 잃고, 성공시 보증금의 10%를 추가로 받습니다.

    - 단, 1원 미만의 단위는 모두 버립니다.
4. 보상은 최종검증이 된 이후 회원이 결과를 인정하면 지급됩니다.


### 목표 인증 기능

1. 회원은 스스로 목표가 달성되었다고 생각하는 경우, "목표달성 인증 게시판"에 인증을 등록합니다. 인증에는 다음 내용들이 포함됩니다.
    - 등록한 목표 (회원이 등록한 목표목록중 선택하는 방식)

    - 인증내용(텍스트, 링크 등 필수 아님)

    - 인증사진(필수)


2. 인증의 검증은 다른 회원들이 합니다. 각 목표에 등록된 보증금의 액수에 따라 필요한 성공검증 횟수가 정해집니다.
    - 10,000 ~ 50,000 : 5회

    - 60,000 ~ 100,000 : 10회

    - 110,000 ~ 500,000 : 20회

    - 510,000 ~ 1,000,000 : 30회

3. 인증 게시글에는 현재 검증 상황이 보여집니다. 단, 누가 어떤 검증을 했는지는 알 수 없습니다. 

3. 실패검증 누적횟수가 성공검증횟수의 90%에 도달할 경우 인증은 실패로 결정되고, 더이상 "목표달성 인증 게시판"에서 볼 수 없게 되고, "내 페이지"에서만 볼 수 있게됩니다. 단, 이 경우 운영진에게 검토를 요청할 수 있습니다.

4. 인증은 인증 등록시점부터 168시간(7일)동안 "목표달성 인증 게시판"에 보여집니다. 단, 기간 내에 검증이 완료될 경우 삭제됩니다.

5. 인증 등록 168시간 동안 검증이 완료되지 않은 인증의 경우 인증을 작성한 회원의 "내정보"에서 볼 수 있고, 운영진에게 검토요청을 할 수 있습니다. 검토결과가 확정되기 전에 충전금의 변화는 없습니다.

6. 목표의 기간 제한에 도달한 시점에 인증이 등록되지 않은 경우 48시간의 유예시간동안 인증이 등록되지 않은 경우 목표달성 실패처리가 됩니다.

7. 회원이 다른 회원의 인증을 검증할 경우 다음과같은 혜택이 주어집니다.

    1. 최종 검증결과가 회원의 검증과 일치할 경우 : 충전 잔액 1000원 추가

    2. 최종 검증결과가 회원의 검증과 다를 경우 : 충전 잔액 0원 추가

    3. 최종 검증결과가 나오지 않은 경우 : 충전 잔액 100원 추가

8. 회원이 다른 회원의 인증에 검증한 것과 최종 검증 결과가 다른 상황이 5회 누적될 경우 회원은 더이상 시스템을 이용 할 수 없습니다. 매 해가 넘어갈 때 마다 누적횟수가 초기화 됩니다. 단, 이전에 5회누적으로 시스템사용이 제한된 경우 해가 바뀌어도 시스템을 사용할 수 없습니다. 
    - 단, 이미 충전 된 금액의 인출은 가능합니다.
    - 이의제기는 할 수 없습니다.


### 통계기능
1. 모든 회원은 각 해에 등록된 목표의 누적 횟수와 달성성공 횟수, 성공비율을 실시간으로 확인 할 수 있습니다.(5분 마다 갱신됩니다.)

2. 모든 회원은 자신이 지금까지 등록한 목표와 성공여부를 확인 할 수 있습니다. 
    - 개별 목표의 목록뿐 아니라, 총 등록 목표개수, 성공한 목표 개수, 실패한 목표 개수, 달성기간내의 목표 개수, 성공(실패)비율등의 통계정보도 확인 할 수 있습니다.

### 시스템 관리 기능
1. 시스템 관리 기능은 웹페이지로만 제공됩니다.

2. 실패검증누적으로 인한 실패에 대한 이의신청을 확인할 수 있습니다.
    - 이 때, 성공으로 검증결과가 바뀌는 경우 즉시 통계에 반영됩니다.

3. 검증기간 초과로 인한 검토요청을 확인 할 수 있습니다.
    - 이때 확정된 결과는 즉시 회원의 보증금, 통계에 반영됩니다.

4. 공지사항을 작성할 수 있습니다.

### 알림 기능
1. 사용자는 알림 기능을 비활성화 할 수 없습니다.(금전이 오고가는 일이므로)

2. 다음과같은 상황에서 특정 사용자에게 알림이 보내집니다.
    - 목표달성 기간제한이 24시간 내인 목표가 발생할 경우

    - 인증의 결과가 정해진 경우

    - 목표달성 기간제한이 지났지만 인증이 등록되지 않은 경우

    - 인증 작성 유예시간이 1시간 남은 경우

    - 각종 검토요청의 답변이 있는 경우

3. 다음과 같은 상황에서 모든 사용자에게 알림이 보내집니다.

    - 공지게시글 작성시 알림 전송을 선택한 경우

4. 사용자에게 보내진 알림은 168시간(7일)동안 "내정보" 에서 확인 할 수 있습니다.
     

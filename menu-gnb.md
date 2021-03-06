# GNB

___

## 기능 설명

- 상단 사용자 공통 메뉴 영역
- 주요 기능
  - 알림 메시지
  - 요금 정보 조회  
  - 메뉴
    - 사용자 계정 표시, 개인 정보 링크, 로그 아웃, 그룹 계정 리스트

___
## URL
- 콘솔 전체 페이지

___
## 관련 Source
- WebContent/d/consolemenu/topMenu.jsp
- WebContent/d/scripts/console.common.220_renewal.js
- WebContent/d/scripts/portal.common.cr.js
- WebContent/g/consolemenu/topMenu.jsp
- WebContent/g/scripts/console.common.220_renewal.js
- WebContent/g/scripts/portal.common.cr.js
- /src/ktcloudportal/epc/servlet/IsSvcSvrProcess.java
- /src/ktcloudportal/epc/servlet/ConsoleMainServlet.java  
- /src/ktcloudportal/epc/database/IsSvcDao.java  
___
## 업무 flow
___
1. 로그인 여부 확인
2. 사용자 유형 확인<br>
: Root 사용자(그룹 Admin), Root 사용자(일반 사용자 : 그룹 사용자가 아니거나 그룹원인 경우), IAM 사용자<br>
3. 사용자 유형에 따른 메뉴 표시<br>
3-1. IAM 사용자<br>
: 알림, 사용자 계정, 로그 아웃 이외의 메뉴는 표시 안함<br>
: 정책상 개인 정보 영역은 접근 못함<br>
3-2. 그룹 Admin<br>
: 모든 메뉴 표시, 그룹내 그룹원 정보 표시함<br>
3-3. 그룹원
: 모든 메뉴 표시, 그룹내 그룹원 정보 표시 안함<br>

___
## 주요 기능 상세
___
### 알림 메시지
- 각 서비스별 Action 이력 조회
- 로그인 후 ~ 로그아웃까지 세션이 유지되는 동안만 유지됨
  - To Be : DB 관리 기능 필요(추가, 삭제, 조회 API 필요)
  
### Database
- DB 사용 없음
- 현재 Session Storage를 이용하여 처리
### API List
- add_noti_message : 알림 메시지 추가
- show_noti_message : 알림 메시지 조회
- delete_noti_message : 알림 메시지 삭제
	- To Be : DB를 통한 추가, 삭제, 조회용 API 추가 필요 

___
### 요금 정보 조회
- 해당 월 현재일까지의 요금 조회
- 해당 월 월말까지의 예상 요금 조회
- 그룹 계정 선택시 해당 계정에 대한 권한 유무 확인
### Database
- TBMB_GROUP
- TBMB_GROUP_USER
- SP : CSP_CONSOLE_SELECT_CHARGE
### API List  
- 요금 조회 : set_charge_info
  - 서블릿 : ConsoleMainServlet
  - command : use_bill_charge 
  - 파라미터 : defaultYYYY: 2022, defaultMM: 05, defaultDD: 03, group_mem_sq:  
  - use_bill_charge(String memSq, String searchDate) : DB를 통한 요금 조회<br>
  - 관련 쿼리 : ConsoleMainDao.use_bill_charge 참조
- 그룹 계정에 대한 권한 체크 : group_auth_check  
  - 서블릿 : IsSvcDao
  - command : group_auth_check 
  - 파라미터 : group_mem_sq
  - 관련 쿼리 : IsSvcDao.groupAuthCheck 참조

___
### 메뉴 표시

### Database
- TBMB_GROUP
- TBMB_GROUP_USER


### API List
- IAM 사용자 판단:Session내 'iamid'로 판단
  - To Be에서 API화 필요<br><br>
- 그룹 user 조회 : getGroupUserSelect
  - 서블릿 : IsSvcSvrProcess
  - command : getGroupUserSelect
  - parameter : memsq
    - 그룹원 조회 결과 1명 이상인 경우 그룹 Admin으로 판단, 조회 결과가 없으면 일반 사용자<br>
	- To Be : 그룹원 조회 결과 첫번째는 Admin, 나머지는 그룹원 순으로 조회 되도록 개발(임창준과장 요청)<br>
	- To Be : 그룹원 정보는 기본적으로 Masking 처리, UnMasking 기능 추가(임창준과장 요청)<br>
  - 관련 쿼리 : IsSvcDao.getGroupUserSelect 참조
- 메뉴 표시
  - topMenu.jsp, console.common.220_renewal.js에 
	- To Be : 메뉴 조회 API 추가 개발
	- 해당 API에서 IAM, 그룹에 따른 권한 처리 후 최종 메뉴 제공<br><br>
___
### 참고 사항
- IAM : IAM 메뉴 참고
- Group : User > Group
- 상품 청약 : User > 결제 정보, All Service 참고
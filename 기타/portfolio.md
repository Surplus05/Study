# 목차  
1. [코인](#코인)
2. [유튜브 프로젝트](#유튜브-프로젝트)
3. [간단한 게시판](#간단한-게시판)
4. [분양 플랫폼](#분양-플랫폼)

## 코인

개발 기간 : 22.6.28 ~ 22.7.14

개발 스택 : React, Firebase

상태 관리 : Redux, Redux-toolkit

깃허브 : https://github.com/Surplus05/coin

데모 링크 : https://surplus05.github.io/coin/exchange

### 기능

1. 코인 검색  
   심볼 혹은 코인명으로 코인의 검색이 가능. 검색창은 무한 스크롤로, 시가총액 순으로 표시. Intersection Observer를 통해 마지막에서 5번째 코인을 보는 경우 추가 코인을 불러와 표시.
2. 코인 조회  
   검색창에서 코인 클릭시 코인명, 코인 심볼, 시가총액, 현재가격,최근 72시간의 가격 차트 표시
3. 코인 매수 / 매도  
   현재가격으로 매수 혹은 매도 가능.
4. Firebase 로그인 / 회원가입  
   Firebase 를 통한 이메일 로그인 / 회원가입 (현재 Firebase API 연결 끊김)

## 화면

<img src="./image/COIN.png"  width="640" height="360">

## 유튜브 프로젝트

개발 기간 : 1.4 ~ 1. 31

개발 스택 : React, TypeScript, Firebase

상태 관리 : Redux, Redux-toolkit

스타일링 : Styled-Components

깃허브 : https://github.com/Surplus05/react-ts

데모 링크 : https://surplus05.github.io/react-ts

### 기능

1. 영상 검색  
   검색 버튼을 누르지 않아도 결과가 미리보기 형태로 표시됨.  
   입력 바뀔때 마다 API 요청을 하면 API 서버에 부담이 크므로, 입력이 변하고 일정시간이 지나야 API 요청을 보내는 Throttle 방식 사용.
2. 바로 시청  
   메인 화면에서 썸네일에 마우스를 일정시간 올리고 있거나, 클릭하는 경우 해당 영상이 바로 재생됨.
3. 같이 시청  
   같이 시청 버튼을 누르는 경우 별도의 페이지에서 영상이 재생.
4. 채팅  
   Firebase 를 이용한 채팅기능 지원.  
   Socket 을 사용한 진짜 실시간은 아니고 재생 시간에 따라 출력해 주어 실시간 처럼 보이게 구현.
5. 시청기록, 설정  
   설정 페이지에서 시청기록 조회, 삭제와 닉네임 변경이 가능

## 화면

<img src="./image/RT (3).png"  width="640" height="690">
<img src="./image/RT (5).png"  width="640" height="690">
<img src="./image/RT (6).png"  width="640" height="690">
<img src="./image/RT (7).png"  width="640" height="690">
<img src="./image/RT (1).png"  width="960" height="540">
  
  
## 간단한 게시판

개발 기간 : 2.6 ~ 2. 14

개발 스택 : React, TypeScript, ExpressJS, Oracle

스타일링 : Styled-Components

깃허브 : https://github.com/Surplus05/express-oracle

데모 링크 : 해외 IP 로 접근하는 경우가 가끔 있어 배포 중단한 상태입니다.

### 기능

1. 게시판  
   게시판 CRUD 기능 + 페이징, 추천 / 비추천 + 댓글 기능 구현
2. 회원가입  
   이메일 / 닉네임 중복확인 기능을 포함한 회원가입.
3. Restful API와 성능최적화  
   [여기](https://github.com/Surplus05/express-oracle/tree/Refactoring)에서 API Restful 변경과 성능최적화를 진행했습니다.

## 화면

<img src="./image/EO (2).png"  width="640" height="690">
<img src="./image/EO (1).png"  width="640" height="690">
<img src="./image/EO (3).png"  width="640" height="690">
<img src="./image/EO (4).png"  width="640" height="690">

## 분양 플랫폼

개발 기간 : 5.8 ~ 6.9  

개발 인원 : 프론트엔드 2, 백엔드 2  

개발 스택 : React, TypeScript, Next.js  

상태 관리 : Recoil  

스타일링 : SCSS

협업 툴 : Notion, Discord, Git

깃허브 : https://github.com/adopt-pet-project/Front-End/tree/master

링크 : https://pet-hub.site/

### 기능 (담당한 프론트엔드 파트)

1. 게시판  
   게시글 CRUD, 게시글 추천, 제목 또는 내용 검색 기능 지원
2. 게시글 댓글  
   댓글 CRUD, 대댓글 작성, 댓글 추천 기능 지원
3. 분양글  
   분양글 CRUD 기능 지원.  
   사진 업로드(비동기 상태관리), 사진 Carousel, 지도 좌표와 주소간 변환기능 지원
4. 채팅  
   분양글에서 문의 버튼 클릭시 해당 작성자와 채팅 지원. 서버와 Socket 통신해 채팅기능 지원
5. 쪽지 전송  
   게시글과 댓글로부터 쪽지 전송 기능 지원   
7. 로그인 / 회원가입과 토큰 관리  
   JWT 토큰을 관리하고 삭제. 로그인 팝업과 회원가입 기능 지원
8. 반응형 레이아웃  
   grid와 flex, media query 를 활용해 반응형 지원.
9. 배포 (프론트 서버)와 CI/CD  
   EC2 Instance 를 통해 배포, S3 CodeDeploy, Github Actions 를 통해 CI/CD 환경 구축.

## 화면

<img src="./image/AP (3).png"  width="960" height="540">
<img src="./image/AP (2).png"  width="640" height="690">
<img src="./image/AP (4).png"  width="640" height="690">
<img src="./image/AP (5).png"  width="640" height="690">
<img src="./image/AP (1).png"  width="640" height="690">
<img src="./image/AP (6).png"  width="640" height="690">
<img src="./image/AP (7).png"  width="640" height="690">
<img src="./image/AP (8).png"  width="640" height="690">
<img src="./image/AP (9).png"  width="640" height="690">
<img src="./image/AP (10).png"  width="640" height="690">

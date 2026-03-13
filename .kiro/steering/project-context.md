---
inclusion: always
---

# 신월중학교 야구 스코어보드 시스템

## 프로젝트 개요
CameraFi Live 방송용 야구 스코어보드 오버레이 시스템.
스마트폰 컨트롤러로 점수/이닝/카운트/베이스/라인업을 조작하면, Firebase Realtime Database를 통해 오버레이 화면에 실시간 반영된다.

- 배포: GitHub Pages (`https://coson35.github.io/score/`)
- 백엔드: Firebase Realtime Database (서버리스)
- 빌드 도구 없음. 순수 HTML/CSS/JS, Firebase SDK는 CDN으로 로드

## 파일 구조 및 역할

| 파일 | 역할 |
|------|------|
| `controller.html` | 메인 조작 화면. 스마트폰에서 접속하여 경기 운영. 로그인(관리자/사용자), 점수/이닝/카운트/베이스 조작, 경기설정, 라인업 관리, 선수 등록, 타자/투수 정보, 오버레이 설정, 기기 잠금, 백업/복구 등 모든 기능 포함 |
| `scoreboard.html` | CameraFi 오버레이용. 하단 바 형태의 스코어보드 표시 (투명 배경). 점수, 이닝, BSO 카운트, 베이스, 타자/투수 정보 표시 |
| `lineup.html` | CameraFi 오버레이용. 수비 위치도(야구장 그림) 또는 타순 리스트 표시 (투명 배경) |
| `firebase-config.js` | Firebase 프로젝트 설정 (apiKey, databaseURL 등). 모든 HTML에서 공유 |
| `upload-roster.html` | 최초 선수 명단 일괄 업로드용 (1회성 유틸리티) |
| `sinwol-logo.png` | 학교 로고 이미지 |
| `SETUP-GUIDE.md` | 전체 설정 및 운영 매뉴얼 |
| `MANUAL-CONTROLLER.md` | 컨트롤러 상세 조작 매뉴얼 |
| `MANUAL-CAMERAFI.md` | CameraFi Live 오버레이 설정 매뉴얼 |

## Firebase 데이터 구조

| 경로 | 용도 |
|------|------|
| `game/` | 현재 경기 상태 (점수, 이닝, BSO, 베이스, 팀명, 로고, visible 등) |
| `lineup/` | 현재 라인업 (포지션별 선수, 타순, 표시모드, visible 등) |
| `savedGames/` | 저장된 경기 프리셋 |
| `savedLineups/` | 저장된 라인업 프리셋 |
| `config/` | 설정 (adminPassword, userPassword, activeGameKey, overlay 설정, lockMinutes) |
| `roster/` | 선수 명단 (이름, 번호, 포지션) |
| `teams/` | 저장된 팀 정보 (이름, 로고) |
| `batterInfo/` | 타자/투수 정보 (타순, 타석결과, 투구수 등) |
| `controlLock/` | 기기 잠금 상태 |

## 주요 기능

- 로그인: SHA-256 해시 비밀번호, 관리자/사용자 2단계
- 기기 잠금: 한 기기에서만 조작 가능하도록 잠금 (다른 기기 차단)
- 오버레이 ON/OFF: 스코어보드와 라인업을 독립적으로 표시/숨김
- 라인업 모드: 수비 위치도 / 타순 리스트 전환
- 타자 정보: 타순별 타석 결과 기록, 표시 위치(좌/중/우) 선택
- 투수 정보: 투구수 카운트
- 오버레이 커스터마이징: 바 크기, 색상 테마, 포지션 위치, 로고 크기 등 세밀 조정
- 팀 자동완성: 한번 등록한 팀은 이름 입력 시 로고 자동 적용
- 백업/복구: Firebase 전체 데이터 JSON 다운로드 및 복원
- 호스트 검증: coson35.github.io, localhost, 127.0.0.1에서만 동작

## 코드 특성

- controller.html이 핵심 파일 (전체 로직의 90% 이상)
- 단일 HTML 파일에 CSS + JS 모두 인라인으로 포함 (외부 파일 분리 없음)
- 변수명이 축약형 (gd=gameData, ld=lineupData, biData=batterInfoData 등)
- Firebase SDK 9.23.0 compat 버전 사용
- 모바일 최적화 (터치 UI, viewport 설정)

## 작업 시 주의사항

1. controller.html 수정 시 파일이 매우 크므로 (1000줄+) 수정할 부분을 정확히 특정해서 작업할 것
2. scoreboard.html과 lineup.html은 오버레이 전용이므로 배경 투명(transparent) 유지 필수
3. Firebase 설정(firebase-config.js)은 실제 운영 중인 DB이므로 변경 금지
4. 호스트 검증 스크립트가 각 HTML 상단에 있음 — 제거하지 말 것
5. 배포는 git push만 하면 GitHub Pages에 자동 반영
6. 이미지 로고는 base64로 Firebase에 저장됨 (500KB 제한)

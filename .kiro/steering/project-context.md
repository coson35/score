---
inclusion: always
---

# 신월중학교 야구 스코어보드 시스템

## 프로젝트 개요
CameraFi Live 방송용 야구 스코어보드 오버레이 시스템.
스마트폰 컨트롤러로 점수/이닝/카운트/베이스/라인업을 조작하면, Firebase Realtime Database를 통해 오버레이 화면에 실시간 반영된다.

- GitHub: `https://github.com/coson35/score.git` (브랜치: main)
- 배포: GitHub Pages (`https://coson35.github.io/score/`)
- 백엔드: Firebase Realtime Database (서버리스)
- 빌드 도구 없음. 순수 HTML/CSS/JS, Firebase SDK는 CDN으로 로드

## 작업 방식
- 코드 수정 후 `git add -A` → `git commit -m "메시지"` → `git push origin main`
- push하면 GitHub Pages에 1~2분 내 자동 배포
- 관리 탭의 "모든 사용자 새로고침" 버튼으로 접속자 강제 새로고침 가능
- 다른 PC에서 작업 시: `git clone https://github.com/coson35/score.git` 후 Kiro로 폴더 열기

## 파일 구조 및 역할

| 파일 | 역할 |
|------|------|
| `controller.html` | 메인 조작 화면 (전체 로직 90%+). 로그인, 점수/이닝/카운트/베이스 조작, 경기설정, 라인업, 선수 등록, 타자/투수 정보, 오버레이 설정(아코디언 UI), 기기 잠금, 백업/복구, 전체 새로고침 |
| `scoreboard.html` | CameraFi 오버레이용 (투명 배경). 하단 스코어보드 바 + 좌측 상단 학교 로고 + 라인업(수비위치/타순) + 타자/투수 정보 바. 모든 오버레이가 이 파일 하나에 통합 |
| `lineup.html` | 별도 라인업 오버레이 (현재 scoreboard.html에 통합되어 있어 사실상 미사용) |
| `firebase-config.js` | Firebase 프로젝트 설정. 모든 HTML에서 공유. 절대 수정 금지 |
| `upload-roster.html` | 최초 선수 명단 일괄 업로드용 (1회성 유틸리티) |
| `sinwol-logo.png` | 학교 로고 이미지 |
| `SETUP-GUIDE.md` | 전체 설정 및 운영 매뉴얼 |
| `MANUAL-CONTROLLER.md` | 컨트롤러 상세 조작 매뉴얼 |
| `MANUAL-CAMERAFI.md` | CameraFi Live 오버레이 설정 매뉴얼 |

## Firebase 데이터 구조

| 경로 | 용도 |
|------|------|
| `game/` | 현재 경기 상태 (점수, 이닝, BSO, 베이스, 팀명, 로고, visible, logoVisible 등) |
| `lineup/` | 현재 라인업 (포지션별 선수, 타순, 표시모드, visible 등) |
| `savedGames/` | 저장된 경기 프리셋 |
| `savedLineups/` | 저장된 라인업 프리셋 |
| `config/` | 설정 (adminPassword, userPassword, activeGameKey, overlay 설정, lockMinutes, appVersion) |
| `roster/` | 선수 명단 (이름, 번호, 포지션) |
| `teams/` | 저장된 팀 정보 (이름, 로고) |
| `batterInfo/` | 타자/투수 정보 (타순, 타석결과, 투구수 등) |
| `controlLock/` | 기기 잠금 상태 |

## 주요 기능

- 로그인: SHA-256 해시 비밀번호, 관리자/사용자 2단계
- 기기 잠금: 한 기기에서만 조작 가능하도록 잠금 (다른 기기 차단)
- 스코어 ON/OFF: 스코어보드 하단 바 표시/숨김
- 로고 ON/OFF: 좌측 상단 학교 로고 독립 토글 (상단 바 스위치)
- 라인업: 수비위치/타순 버튼이 토글 방식 (터치→ON, 다시 터치→OFF). 별도 ON/OFF 버튼 없음
- 이닝 전환: 화살표가 초/말 포함 순서 진행 (▶: 초→말→다음회초, ◀: 역순). 전환 시 BSO/베이스 자동 초기화
- 타자 정보: 타순별 타석 결과 기록, 표시 위치(좌/중/우) 선택. OFF 상태에서도 수정 가능
- 투수 정보: 투구수 카운트. 로스터에서 투수(P) 우선 표시, 전체 선수도 선택 가능
- 오버레이 커스터마이징: 아코디언 UI로 섹션별 관리 (하단바/항목간격/상하위치/색상/라인업/로고/포지션위치/테마). 섹션별 초기화 버튼
- 팀 자동완성: 한번 등록한 팀은 이름 입력 시 로고 자동 적용
- 백업/복구: Firebase 전체 데이터 JSON 다운로드 및 복원
- 전체 새로고침: 관리 탭에서 모든 접속자 강제 새로고침 (config/appVersion 변경 감지)
- 호스트 검증: coson35.github.io, localhost, 127.0.0.1에서만 동작. 다른 서버 이전 시 각 HTML의 호스트 검증 코드 수정 필요

## 코드 특성

- controller.html이 핵심 파일 (1000줄+, CSS+JS 인라인)
- 변수명 축약형: gd=gameData, ld=lineupData, biData=batterInfoData, ovCfg=overlayConfig
- Firebase SDK 9.23.0 compat 버전 (CDN)
- 모바일 최적화 (터치 UI, viewport 설정)
- 오버레이 설정은 슬라이더 조작 시 autoSaveOv()로 자동 저장 (300ms 디바운스)
- OV_DEFAULTS 객체에 모든 오버레이 기본값 정의

## 작업 시 주의사항

1. controller.html 수정 시 파일이 매우 크므로 수정할 부분을 정확히 특정해서 작업할 것
2. scoreboard.html은 오버레이 전용이므로 배경 투명(transparent) 유지 필수
3. firebase-config.js는 실제 운영 중인 DB이므로 변경 금지
4. 호스트 검증 스크립트가 각 HTML 상단에 있음 — 제거하지 말 것
5. 배포는 git push만 하면 GitHub Pages에 자동 반영
6. 이미지 로고는 base64로 Firebase에 저장됨 (500KB 제한)
7. CameraFi 오버레이는 PC 브라우저와 렌더링이 다를 수 있음 — 항목 상하 위치 조절로 미세 조정
8. 수정 후 관리 탭의 "모든 사용자 새로고침"으로 접속자에게 반영 가능

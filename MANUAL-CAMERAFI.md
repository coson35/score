# CameraFi Live 스코어보드 설정 매뉴얼

## 오버레이 URL

| 구분 | URL |
|------|-----|
| 스코어보드 | `https://coson35.github.io/score/scoreboard.html` |
| 라인업 | `https://coson35.github.io/score/lineup.html` |

---

## CameraFi Live 설정 방법

### 1. 앱 실행
- CameraFi Live 앱을 실행합니다

### 2. 오버레이 추가
1. 방송 화면에서 오버레이 메뉴 진입
2. "웹 URL" 오버레이 추가 선택

### 3. 스코어보드 URL 등록
1. URL 입력란에 아래 주소 입력:
   ```
   https://coson35.github.io/score/scoreboard.html
   ```
2. 스코어보드는 화면 하단에 가로 바 형태로 표시됩니다
3. 배경이 투명하므로 방송 화면 위에 자연스럽게 겹쳐집니다

### 4. 라인업 URL 등록
1. 새 웹 URL 오버레이를 하나 더 추가
2. URL 입력란에 아래 주소 입력:
   ```
   https://coson35.github.io/score/lineup.html
   ```
3. 라인업은 화면 중앙에 표시됩니다

### 5. 오버레이 위치/크기
- CameraFi 무료 버전에서는 오버레이 위치/크기 조절이 제한됩니다
- 오버레이는 기본 위치에 표시됩니다

---

## 방송 중 오버레이 제어

오버레이 표시/숨김은 CameraFi에서 하지 않습니다.
별도 스마트폰의 컨트롤러에서 제어합니다.

### 컨트롤러 주소
```
https://coson35.github.io/score/controller.html
```

### 제어 방법
- 컨트롤러 경기 탭 상단에 ON/OFF 버튼이 있습니다
- "스코어 ON" → 스코어보드 표시
- "스코어 OFF" → 스코어보드 숨김
- "라인업 ON" → 라인업 표시
- "라인업 OFF" → 라인업 숨김

OFF 상태에서는 오버레이가 완전히 투명해져서 방송 화면에 아무것도 보이지 않습니다.

---

## 방송 구성 예시

```
┌─────────────────────────────┐
│                             │
│      카메라 영상             │
│                             │
│    ┌─────────────────┐      │
│    │  라인업 오버레이  │      │  ← URL 2 (필요할 때만 ON)
│    │  (lineup.html)  │      │
│    └─────────────────┘      │
│                             │
│ ┌─────────────────────────┐ │
│ │ 스코어보드 (scoreboard) │ │  ← URL 1 (경기 중 항상 ON)
│ └─────────────────────────┘ │
└─────────────────────────────┘

+ 별도 스마트폰: 컨트롤러 (controller.html)
```

---

## 주의사항

- CameraFi에 URL을 등록한 후 방송을 시작해야 합니다
- 오버레이가 로딩되려면 인터넷 연결이 필요합니다
- 컨트롤러와 오버레이는 Firebase를 통해 실시간 동기화됩니다
- 컨트롤러에서 조작하면 약 0.5초 이내에 오버레이에 반영됩니다
- 방송 중 스마트폰 화면이 꺼지면 오버레이가 멈출 수 있으니 화면 꺼짐 방지를 설정하세요

---

## Firebase 보안 규칙 (최초 1회)

Firebase 테스트 모드는 30일 후 만료됩니다.
Firebase 콘솔 → Realtime Database → 규칙 탭에서 아래로 변경:

```json
{
  "rules": {
    "game": { ".read": true, ".write": true },
    "lineup": { ".read": true, ".write": true },
    "savedGames": { ".read": true, ".write": true },
    "savedLineups": { ".read": true, ".write": true },
    "config": { ".read": true, ".write": true },
    "roster": { ".read": true, ".write": true },
    "teams": { ".read": true, ".write": true }
  }
}
```

이렇게 변경하면 만료 없이 계속 사용 가능합니다.

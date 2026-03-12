# 스코어보드 설정 가이드 (10분이면 끝)

## 1단계: Firebase 프로젝트 만들기

1. https://console.firebase.google.com 접속 (구글 로그인)
2. "프로젝트 추가" 클릭
3. 프로젝트 이름: `baseball-scoreboard` (아무거나 OK)
4. Google 애널리틱스 → 사용 안함 → "프로젝트 만들기"

## 2단계: Realtime Database 만들기

1. 왼쪽 메뉴 → "빌드" → "Realtime Database"
2. "데이터베이스 만들기" 클릭
3. 위치: United States (us-central1) 선택
4. 보안 규칙: "테스트 모드에서 시작" 선택 → "사용 설정"

## 3단계: 웹앱 등록 + 설정값 복사

1. 왼쪽 상단 ⚙️ → "프로젝트 설정"
2. 아래로 스크롤 → "내 앱" → 웹 아이콘(</>) 클릭
3. 앱 닉네임: `scoreboard` → "앱 등록"
4. Firebase SDK 설정값이 나옴 → 아래 값들을 복사:
   - apiKey
   - authDomain
   - databaseURL
   - projectId
   - storageBucket
   - messagingSenderId
   - appId

5. `firebase-config.js` 파일을 열어서 YOUR_XXX 부분을 복사한 값으로 교체

## 4단계: GitHub Pages로 무료 호스팅

1. https://github.com 접속 (계정 없으면 가입)
2. 새 저장소(Repository) 만들기
   - 이름: `baseball-scoreboard`
   - Public 선택
   - "Create repository"
3. 파일 업로드:
   - "uploading an existing file" 클릭
   - `scoreboard.html`, `lineup.html`, `controller.html`, `firebase-config.js` 4개 파일 드래그
   - "Commit changes"
4. Settings → Pages → Source: "main" 브랜치 → Save
5. 1~2분 후 URL 생성됨:
   - `https://[내아이디].github.io/baseball-scoreboard/scoreboard.html`
   - `https://[내아이디].github.io/baseball-scoreboard/lineup.html`
   - `https://[내아이디].github.io/baseball-scoreboard/controller.html`

## 5단계: 카메라파이 앱에 URL 넣기

- URL 1: `https://[내아이디].github.io/baseball-scoreboard/scoreboard.html`
- URL 2: `https://[내아이디].github.io/baseball-scoreboard/lineup.html`

## 6단계: 컨트롤러 접속

- 다른 스마트폰 브라우저에서:
  `https://[내아이디].github.io/baseball-scoreboard/controller.html`
- 첫 접속 시 비밀번호 설정 (이후 같은 비밀번호로 로그인)

## 끝! 🎉

---

## 나중에 보안 규칙 변경 (선택)

테스트 모드는 30일 후 만료됩니다. 그 전에 Firebase 콘솔 → Realtime Database → 규칙 탭에서:

```json
{
  "rules": {
    "game": {
      ".read": true,
      ".write": true
    },
    "lineup": {
      ".read": true,
      ".write": true
    },
    "savedGames": {
      ".read": true,
      ".write": true
    },
    "config": {
      ".read": true,
      ".write": true
    }
  }
}
```

이렇게 바꾸면 만료 없이 계속 사용 가능합니다.

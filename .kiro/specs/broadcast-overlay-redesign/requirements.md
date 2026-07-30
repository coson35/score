# Requirements Document

## Introduction

기존 방송 오버레이(`scoreboard.html`)는 실제 방송에 계속 사용되므로 수정하지 않는다. 대신 방송형 하단 스코어보드 바 1종을 담은 새 오버레이 파일(`scoreboard2.html`)을 독립적으로 추가한다.

이번 스펙이 해결하는 문제는 두 가지다.

1. **해상도별 표시 불일치**: 기존 오버레이는 모든 치수가 고정 픽셀이라 CameraFi Live(스마트폰 직접 방송 / 캠코더 연결 방송)와 PC 브라우저에서 요소의 위치·크기가 다르게 보인다. 새 오버레이는 1920x1080 기준 좌표계에 레이아웃을 구성하고 실제 뷰포트에 맞춰 전체를 단일 배율로 확대·축소하여 환경과 무관하게 동일한 화면 대비 위치·크기로 보이게 한다.
2. **방송형 하단바 디자인**: 참고 이미지 스타일의 얇은 전체 폭 하단바(좌측 경기명 사선 블록 / 중앙 스코어 영역 / 카운트 박스 / 우측 브랜딩 사선 블록)를 구현한다.

새 오버레이의 설정은 Firebase의 별도 경로(`config/overlay2`)에 저장하며, 경기 상태(`game/`)는 읽기 전용으로 기존과 동일하게 사용한다. 라인업 화면, 타자·투수 정보 바, 좌측 상단 학교 로고, 기존 스킨, 테마 저장/불러오기는 이번 범위에서 제외한다.

## Glossary

- **New_Overlay**: 새로 추가하는 방송형 오버레이 페이지(`scoreboard2.html`)
- **Legacy_Overlay**: 기존 오버레이 페이지(`scoreboard.html`)와 `controller.html`의 기존 오버레이 관련 코드
- **Controller**: 조작 화면(`controller.html`)
- **Stage**: New_Overlay 내부에서 1920x1080 기준 좌표계로 레이아웃을 구성하는 최상위 컨테이너
- **Scale_Factor**: 뷰포트 크기를 1920x1080으로 나눈 값 중 작은 값. Stage 전체에 적용하는 단일 배율
- **Bottom_Bar**: New_Overlay의 화면 하단 전체 폭 스코어보드 바
- **Count_Box**: Bottom_Bar 우측의 볼·스트라이크·아웃 표시 영역
- **Branding_Block**: Bottom_Bar 최우측의 사선 처리된 브랜딩 문구 영역(2줄)
- **Overlay2_Config**: Firebase `config/overlay2` 경로에 저장되는 New_Overlay 전용 설정
- **Game_State**: Firebase `game/` 경로의 경기 상태 데이터

## Requirements

### Requirement 1: 기존 오버레이 무영향 보장

**User Story:** 방송 운영자로서, 새 오버레이를 추가하는 작업이 실제 방송에 쓰이는 기존 오버레이에 어떤 영향도 주지 않기를 원한다. 그래야 검증 전에도 안심하고 기존 오버레이로 방송할 수 있다.

#### Acceptance Criteria

1. THE New_Overlay SHALL 기존 파일과 분리된 새 파일 `scoreboard2.html` 로 구현된다
2. THE Legacy_Overlay SHALL 이번 작업 전후로 동일한 코드를 유지한다
3. THE New_Overlay SHALL 설정값을 Firebase `config/overlay2` 경로에서만 읽는다
4. THE Controller SHALL New_Overlay 설정값을 Firebase `config/overlay2` 경로에만 기록한다
5. THE Controller SHALL 기존 오버레이 설정 섹션을 유지한 상태로 New_Overlay 전용 설정 섹션을 추가한다
6. THE New_Overlay SHALL Game_State를 읽기 전용으로 사용한다
7. THE New_Overlay SHALL `firebase-config.js` 를 수정하지 않고 그대로 참조한다

### Requirement 2: 해상도 독립 렌더링

**User Story:** 방송 운영자로서, 스마트폰 CameraFi Live 방송, 캠코더 연결 방송, PC 브라우저에서 오버레이가 같은 모습으로 보이기를 원한다. 그래야 환경별로 위치를 다시 조정하지 않아도 된다.

#### Acceptance Criteria

1. THE New_Overlay SHALL 모든 레이아웃 치수를 1920x1080 기준 좌표계의 Stage 내부에 정의한다
2. WHEN New_Overlay가 로드되면, THE New_Overlay SHALL 뷰포트 크기로부터 Scale_Factor를 계산하고 Stage 전체에 단일 배율로 적용한다
3. WHEN 뷰포트 크기가 변경되면, THE New_Overlay SHALL Scale_Factor를 다시 계산하여 Stage에 적용한다
4. THE New_Overlay SHALL Stage의 가로세로 비율을 16:9로 유지한다
5. WHEN 서로 다른 뷰포트 해상도에서 New_Overlay를 표시하면, THE New_Overlay SHALL Bottom_Bar의 모든 요소를 화면 대비 동일한 상대 위치·상대 크기로 표시한다
6. THE New_Overlay SHALL 배경을 투명하게 유지한다

### Requirement 3: 방송형 하단바 레이아웃

**User Story:** 방송 운영자로서, 참고 이미지와 같은 방송형 하단바를 원한다. 그래야 중계 화면이 전문적으로 보인다.

#### Acceptance Criteria

1. THE Bottom_Bar SHALL 화면 하단에 전체 폭을 차지하는 단일 바로 표시된다
2. THE Bottom_Bar SHALL 좌측에 사선으로 처리된 파란색 블록을 배치하고 그 안에 경기명을 표시한다
3. THE Bottom_Bar SHALL 중앙 어두운 회색 영역에 이닝 박스, 원정팀 로고, 원정팀명, 원정팀 점수, 베이스 다이아몬드, 홈팀 점수, 홈팀명, 홈팀 로고를 좌에서 우 순서로 배치한다
4. THE Bottom_Bar SHALL 중앙 영역 우측에 Count_Box를 배치하고 볼·스트라이크 카운트와 아웃 표시를 함께 표시한다
5. THE Bottom_Bar SHALL 최우측에 사선으로 처리된 빨간색 Branding_Block을 배치하고 브랜딩 문구를 2줄로 표시한다
6. THE Bottom_Bar SHALL 원정팀 점수를 청록색, 홈팀 점수를 노란색으로 표시한다

### Requirement 4: 경기 상태 실시간 반영

**User Story:** 방송 운영자로서, 컨트롤러에서 점수나 카운트를 조작하면 새 오버레이에 즉시 반영되기를 원한다. 그래야 경기 진행과 화면이 어긋나지 않는다.

#### Acceptance Criteria

1. WHEN Game_State가 변경되면, THE New_Overlay SHALL 경기명, 팀명, 팀 로고, 점수, 이닝, 볼, 스트라이크, 아웃, 베이스 표시를 변경된 값으로 갱신한다
2. THE New_Overlay SHALL `inningHalf` 값이 `top` 이면 이닝 박스에 상승 삼각형 기호를, `bottom` 이면 하강 삼각형 기호를 이닝 숫자와 함께 표시한다
3. THE New_Overlay SHALL `base1`, `base2`, `base3` 가 주자 있음을 나타내는 값일 때 해당 베이스 다이아몬드를 채워진 상태로 표시한다
4. WHILE Game_State의 `visible` 값이 거짓이면, THE New_Overlay SHALL Bottom_Bar를 숨긴다
5. IF 팀 로고 데이터가 없으면, THEN THE New_Overlay SHALL 로고 자리를 비운 상태로 나머지 항목의 배치를 유지한다
6. IF Firebase 연결이 끊어지면, THEN THE New_Overlay SHALL 마지막으로 수신한 Game_State 값을 계속 표시한다

### Requirement 5: 새 오버레이 전용 설정

**User Story:** 방송 운영자로서, 브랜딩 문구와 하단바의 크기·위치를 컨트롤러에서 조정하기를 원한다. 그래야 경기나 시즌이 바뀔 때 파일을 수정하지 않고 대응할 수 있다.

#### Acceptance Criteria

1. THE Controller SHALL New_Overlay 전용 설정 섹션을 기존 오버레이 설정과 동일한 아코디언 UI 방식으로 제공한다
2. THE Controller SHALL Branding_Block의 1줄 문구와 2줄 문구를 입력·수정할 수 있는 입력 항목을 제공한다
3. WHEN 사용자가 New_Overlay 설정값을 변경하면, THE Controller SHALL 300ms 디바운스 후 Overlay2_Config에 자동 저장한다
4. WHEN Overlay2_Config가 변경되면, THE New_Overlay SHALL 변경된 설정을 화면에 반영한다
5. THE Controller SHALL New_Overlay 설정 섹션에 초기화 버튼을 제공하고, 버튼이 눌리면 Overlay2_Config를 기본값으로 되돌린다
6. IF Overlay2_Config가 존재하지 않으면, THEN THE New_Overlay SHALL 내장된 기본 설정값으로 렌더링한다

### Requirement 6: 운영 환경 제약 준수

**User Story:** 개발자로서, 새 오버레이가 기존 프로젝트의 배포·보안 방식을 그대로 따르기를 원한다. 그래야 별도 도구나 절차 없이 운영할 수 있다.

#### Acceptance Criteria

1. THE New_Overlay SHALL 기존 파일과 동일한 호스트 검증 스크립트를 포함하고 `coson35.github.io`, `localhost`, `127.0.0.1` 에서만 동작한다
2. IF 허용되지 않은 호스트에서 New_Overlay가 열리면, THEN THE New_Overlay SHALL 오버레이 내용을 표시하지 않는다
3. THE New_Overlay SHALL 빌드 도구 없이 동작하는 단일 HTML 파일로 구성되고 CSS와 JavaScript를 인라인으로 포함한다
4. THE New_Overlay SHALL Firebase SDK 9.23.0 compat 버전을 CDN으로 로드한다
5. THE New_Overlay SHALL GitHub Pages 배포 후 별도 설정 없이 CameraFi Live 오버레이 URL로 사용할 수 있다

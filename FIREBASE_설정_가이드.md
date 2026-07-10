# Firebase 설정 가이드 — 나의 중독 나무 만들기

이 문서대로 따라 하면 `index.html`이 학생들끼리 실시간으로 함께 쓰는 웹사이트가 돼. 전 과정 15분 내외, 비용은 무료야(교실 규모 사용량은 Firebase 무료 등급으로 충분함).

## 1단계. Firebase 프로젝트 만들기

1. https://console.firebase.google.com 접속 (구글 계정으로 로그인)
2. **프로젝트 추가** 클릭 → 프로젝트 이름 입력 (예: `addiction-tree`) → 계속
3. Google 애널리틱스는 **사용 안 함**으로 두고 진행해도 무방
4. 프로젝트 생성 완료까지 30초 정도 대기

## 2단계. Realtime Database 만들기

1. 왼쪽 메뉴에서 **빌드 → Realtime Database** 클릭
2. **데이터베이스 만들기** 클릭
3. 위치는 `asia-southeast1` (싱가포르, 한국과 가까움) 선택
4. 보안 규칙은 **테스트 모드로 시작** 선택 → 사용 설정

> 테스트 모드는 30일 후 자동으로 잠기니, 아래 3단계에서 규칙을 직접 설정해두는 걸 추천해.

## 3단계. 보안 규칙 설정 (교실용 간단 버전)

Realtime Database 화면에서 **규칙** 탭 클릭 후 아래처럼 입력하고 **게시**:

```json
{
  "rules": {
    "notes": {
      ".read": true,
      ".write": true
    }
  }
}
```

> ⚠️ 이 규칙은 링크를 아는 누구나 읽고 쓸 수 있게 열어두는 **수업용 간단 설정**이야. 학생들끼리 자유롭게 글을 올려야 하는 활동 특성상 이렇게 여는 게 맞지만, 활동이 끝나면 아래 "활동 종료 후" 섹션대로 데이터를 정리하는 걸 추천해.

## 4단계. 웹앱 등록하고 설정값 복사하기

1. 왼쪽 위 **⚙️ 프로젝트 설정** 클릭
2. 아래로 스크롤 → **내 앱** 섹션에서 **</> (웹)** 아이콘 클릭
3. 앱 닉네임 입력 (예: `addiction-tree-web`) → **Firebase Hosting 설정 체크 안 해도 됨** → 앱 등록
4. 화면에 나오는 `firebaseConfig` 객체를 그대로 복사

```js
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "addiction-tree-xxxxx.firebaseapp.com",
  databaseURL: "https://addiction-tree-xxxxx-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "addiction-tree-xxxxx",
  storageBucket: "addiction-tree-xxxxx.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
};
```

## 5단계. index.html에 붙여넣기

`index.html` 파일을 열어서 상단 근처 `firebaseConfig` 부분을 찾아 4단계에서 복사한 값으로 **전체 교체**해줘. (파일 안에 `"여기에_..."` 라고 적힌 부분이 그 자리야.)

## 6단계. 배포하기

아무 정적 호스팅에나 올리면 돼. 가장 쉬운 방법 두 가지:

**A. Firebase Hosting (같은 프로젝트라 가장 간단)**
```
npm install -g firebase-tools
firebase login
firebase init hosting   # public 폴더로 index.html이 있는 폴더 지정
firebase deploy
```
배포 후 `https://프로젝트ID.web.app` 같은 링크가 생김 → 이 링크로 QR코드 생성

**B. GitHub Pages / Netlify**
- `index.html`을 GitHub 저장소에 올리고 Settings → Pages에서 활성화
- 또는 netlify.com에 파일을 그대로 드래그 앤 드롭 (계정 없이도 가능)

## 활동 종료 후

- 결과를 보존하고 싶다면 Realtime Database 화면에서 **데이터 내보내기(JSON)** 로 백업
- 더 이상 안 쓸 거면 규칙을 다시 잠그거나(`.read/.write: false`), 프로젝트 자체를 삭제해도 됨

## 문제 해결

| 증상 | 원인 |
|---|---|
| 화면 상단에 "설정 필요" 경고가 계속 보임 | firebaseConfig 값이 실제 값으로 안 바뀜 |
| "연결 오류" 표시 | 보안 규칙이 아직 테스트 모드가 아니거나 위 3단계 규칙 미적용 |
| 글이 하나도 안 보임 | 학생들이 같은 databaseURL(=같은 프로젝트)에 접속 중인지 확인 |

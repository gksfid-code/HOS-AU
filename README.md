# Field Log — GitHub + Firebase 배포 가이드 (카드 없이, Storage 미사용 버전)

이 버전은 Firebase Storage를 쓰지 않아요(2024년 말부터 Storage는 Blaze 요금제=카드 등록이 필요해졌어요). 대신 사진을 작게 압축해서 Firestore 문서 안에 직접 저장합니다. 화질은 조금 낮아지지만(가로/세로 최대 900px 정도) 카드 등록 없이 무료로 쓸 수 있어요.

## 1. Firebase 프로젝트 만들기
1. https://console.firebase.google.com 접속 → "프로젝트 추가"
2. 프로젝트 이름 아무거나 (예: field-log)
3. 왼쪽 메뉴에서 **Firestore Database** → "데이터베이스 만들기" → 위치는 아무거나 (asia-northeast3 서울이 가까움) → 처음엔 "테스트 모드"로 시작해도 됩니다 (3번에서 규칙을 직접 덮어쓸 거예요)
4. 왼쪽 메뉴의 **Storage는 건너뜁니다.** (카드 없이 진행하는 버전이라 필요 없어요)
5. 왼쪽 위 톱니바퀴 → 프로젝트 설정 → 아래로 스크롤 → "웹 앱 추가"(</> 아이콘) → 닉네임 아무거나 → 등록
6. 화면에 뜨는 `firebaseConfig = { apiKey: ..., ... }` 값을 통째로 복사

## 2. 코드에 설정 값 붙여넣기
`index.html` 상단의 아래 부분을 방금 복사한 값으로 바꿔주세요.

```js
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```
> storageBucket 값은 안 써도 상관없어요(그냥 남겨둬도 무해합니다).

## 3. 보안 규칙 설정
Firebase 콘솔 → Firestore Database → **규칙** 탭 → `firestore.rules` 내용을 그대로 붙여넣고 "게시".

지금 규칙은 `allow read, write: if true`라서, 링크(그리고 firebaseConfig)를 아는 사람은 누구나 읽고 쓸 수 있어요. 친구 둘이서만 쓸 거라 링크를 다른 곳에 공개하지만 않으면 충분합니다.

## 4. GitHub에 올리기 / 업데이트
이미 GitHub 저장소를 만들었다면, 수정된 `index.html`만 다시 올리면 돼요.
```bash
git add .
git commit -m "use firestore-only storage (no blaze needed)"
git push
```
처음 올리는 거라면:
```bash
cd field-log-firebase
git init
git add .
git commit -m "Field Log first version"
git branch -M main
git remote add origin https://github.com/<내계정>/field-log.git
git push -u origin main
```

## 5. 배포
### 방법 A. GitHub Pages (제일 쉬움)
저장소 → Settings → Pages → Source를 "Deploy from a branch" → main / (root) → Save. 몇 분 뒤 `https://<내계정>.github.io/field-log/` 주소가 생겨요.

### 방법 B. Firebase Hosting
```bash
npm install -g firebase-tools
firebase login
firebase init hosting   # public 디렉터리는 현재 폴더(.), 싱글 페이지 앱: No
firebase deploy
```

## 6. 친구에게 공유
생성된 주소를 보내주면 끝! 같은 Firestore를 보고 있어서 한 명이 사진을 올리면 다른 사람 화면에도 실시간으로 반영돼요.

## 참고 / 한계
- 사진 한 장은 최대 900px, 용량 약 0.5~0.6MB 이하로 자동 압축돼요 (Firestore 문서 1개 제한이 1MB라서요).
- 나중에 화질을 더 높이고 싶거나 Storage를 쓰고 싶어지면, Blaze 요금제로 업그레이드 후 이전에 드린 Storage 버전 코드로 바꾸면 됩니다 (무료 한도 안에서는 실제 비용이 거의 안 나가요).
- 문제가 생기면 브라우저 개발자 도구(콘솔)에 Firebase 에러 메시지가 뜨니 확인해보세요.

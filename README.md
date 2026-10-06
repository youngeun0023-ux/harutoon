# 영은의 하루툰 🌷

GitHub Pages에서 Google Drive의 비공개 그림일기를 달력으로 읽습니다. 개인 글·그림·토큰은 저장소에 넣지 않습니다. Sites는 사용하지 않습니다.

## Google 연결 설정

1. Google Cloud Console에 하루툰 소유 계정으로 로그인하고 `하루툰` 프로젝트를 만듭니다.
2. 해당 프로젝트에서 Google Drive API를 사용 설정합니다.
3. Google Auth Platform에서 앱 이름·지원 이메일·개발자 이메일을 설정합니다. 기관 내부용으로 만들 수 있으면 Internal을 선택합니다. External / Testing을 사용할 경우 하루툰 소유 계정만 테스트 사용자로 추가합니다. 기관 정책으로 차단될 경우 기관 관리자의 앱 승인이 필요할 수 있습니다.
4. Data Access에 `openid`, `https://www.googleapis.com/auth/userinfo.email`, `https://www.googleapis.com/auth/drive.readonly` 범위를 추가합니다. Drive 읽기 권한은 전체 Drive에 적용되며 폴더만으로 제한되는 권한은 아닙니다. 앱은 설정된 하루툰 폴더와 그 안의 diaries/images만 조회합니다.
5. Clients에서 Web application OAuth 클라이언트를 만듭니다. Authorized JavaScript origins: `https://youngeun0023-ux.github.io` (경로 없이). 이 앱은 GIS 팝업 토큰 방식이므로 리디렉션 URI는 필요하지 않습니다.
6. 홈페이지의 연결 설정에 공개 클라이언트 ID를 입력합니다. 다른 기기에서도 설정 없이 이용하려면 `config.js`의 `googleClientId`에 같은 공개 ID를 기입합니다. Client secret은 만들거나 제출하지 않습니다.
7. Google로 로그인하고 Drive 읽기 동의를 완료합니다. 계정 이메일이 `config.js`의 allowedEmail과 일치하고 Google이 이메일을 검증했을 때만 기록을 읽습니다.

실제 로그인과 API 연결은 OAuth 클라이언트 생성 후 확인해야 합니다. Testing 모드와 기관 정책에 따라 재동의가 필요할 수 있습니다.

## 저장 형식

하루툰 루트 또는 `diaries` 하위에 `YYYY-MM-DD.json`을 저장합니다. 그림은 루트 또는 `images` 하위에 PNG/JPEG/WebP로 저장합니다.

```json
{
  "format": "harutoon-entry",
  "version": 1,
  "title": "오늘의 제목",
  "story": "오늘 있었던 일",
  "mood": "😊 행복해",
  "imageId": "GOOGLE_DRIVE_IMAGE_FILE_ID",
  "updatedAt": "2026-10-06T12:00:00Z"
}
```

그림은 imageId로 연결하거나 `imageFile`에 해당 폴더 내 그림 파일명을 기록합니다. 이전 백업의 PNG/JPEG/WebP data URL인 `image`도 읽습니다. 날짜별 1개 기록이며 동일 날짜가 중복되면 오류로 알립니다.

홈페이지는 읽기 및 전체 백업 기능을 제공합니다. 글·그림 저장은 ChatGPT의 Google Drive 연결로 수행하며, 저장 후 홈페이지에서 새로고침합니다. 자동 동기화나 홈페이지에서의 편집·삭제 기능은 없습니다.

## 이전 OneDrive 기록 이전

`onedrive-backup.html`은 기존 OneDrive 앱과 설정을 보존한 이전용 화면입니다. 기존 OneDrive 또는 기존 브라우저 기록을 불러와 전체 백업 JSON을 받습니다. OneDrive 기록은 먼저 OneDrive로 연결해야 백업됩니다. 기존 브라우저 기록만 백업하려면 OneDrive를 연결하지 않습니다.

백업을 ChatGPT 대화에 첨부하고 하루툰 Google Drive로 옮겨달라고 요청합니다. 그림까지 포함된 백업인지 확인한 뒤, 날짜별 JSON과 그림을 Google Drive로 업로드하고 홈페이지에서 확인합니다. 기존 날짜의 기록은 확인 없이 덮어쓰지 않으며 OneDrive 원본은 삭제하지 않습니다. 이전 화면의 OneDrive imageId를 Google Drive imageId로 복사해서는 안 됩니다. 그림 바이트를 먼저 다운로드하고 Google Drive의 새 ID로 연결해야 합니다.

## 개인정보와 로그인

GitHub Pages의 화면·소스는 공개입니다. 일기 내용은 비공개 Google Drive에서 Google API의 인증·파일 권한 검사 후 전달됩니다. 화면의 이메일 검사만이 보안 경계는 아닙니다. Drive 폴더를 다른 사람이나 링크에 공유하지 마세요.

토큰은 메모리에만 저장합니다. 페이지 재접속 시 다시 로그인해야 합니다. 토큰 만료·로그아웃 때 화면과 그림 Blob URL을 지웁니다. 로컬 저장소에는 공개 클라이언트 ID만 저장합니다. 이전용 OneDrive 화면은 기존 브라우저 저장소와 Microsoft 세션을 사용합니다.

## 검증

문법 검사와 모의 DOM/API에서 로그인 전 기록 숨김, 다른 계정 거부, 정상 계정의 달력 표시, 로그아웃 후 제거, 토큰 미저장을 확인했습니다. 실제 브라우저의 그림 표시 검사는 OAuth 설정 후 진행합니다. 실제 Google OAuth와 실제 OneDrive 이전은 사용자 설정·백업 확보 후 별도로 확인합니다.

공식 참고: https://developers.google.com/identity/oauth2/web/guides/use-token-model · https://developers.google.com/workspace/drive/api/guides/api-specific-auth

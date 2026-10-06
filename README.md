# 영은의 하루툰 🌷

달력으로 모아보는 그림일기. 그림과 일기 내용은 이 공개 저장소에 들어 있지 않습니다.

## 홈페이지 열기

GitHub 저장소의 **Settings → Pages → Build and deployment**에서:

1. Source: **Deploy from a branch**
2. Branch: **main**, Folder: **/(root)**
3. **Save**

배포가 끝나면 https://youngeun0023-ux.github.io/harutoon/ 에 접속합니다.
GitHub Pages 자체는 공개 화면이지만, OneDrive에 저장된 일기는 로그인한 사용자만 읽습니다.

## 학교 OneDrive 연결

학교 계정에서 앱 등록이나 사용자 동의가 허용되어야 합니다. 차단되어 있다면 학교 Microsoft 365 담당자의 도움이 필요합니다. 학교 저장소를 사용할 수 있는 기간과 정책도 확인해주세요.

1. [Microsoft Entra 관리센터](https://entra.microsoft.com/)에 학교 계정으로 로그인합니다.
2. **Identity → Applications → App registrations → New registration**을 엽니다.
3. 이름: `Harutoon`. 계정 유형: **이 조직 디렉터리의 계정만**(단일 테넌트).
4. Redirect URI 플랫폼: **Single-page application (SPA)**.
5. URI: `https://youngeun0023-ux.github.io/harutoon/` (마지막 `/` 포함).
6. 등록 후 **Application (client) ID**와 **Directory (tenant) ID**를 복사합니다.
7. **API permissions → Add a permission → Microsoft Graph → Delegated permissions → Files.ReadWrite**를 추가합니다. 앱에서 사용자 프로필을 읽지 않으므로 기본 User.Read는 필요 없습니다. Application 권한은 쓰지 않습니다.
8. 홈페이지의 **연결 설정**에 두 ID를 입력하고 저장합니다.
9. **학교 OneDrive 연결**을 누르고 Microsoft 로그인·권한 동의를 진행합니다.

클라이언트 비밀(client secret)은 만들거나 입력하지 않습니다. 암호·토큰은 GitHub에 올리지 않습니다.
Files.ReadWrite는 로그인한 사용자의 파일을 읽고 쓸 수 있는 권한입니다. 이 앱의 코드는 OneDrive의 `Harutoon/diaries`, `Harutoon/images` 폴더만 사용하지만, Microsoft 권한 자체가 폴더에 한정되지는 않습니다.
권한 목록이 제대로 설정되어 있어도 학교의 사용자 동의 제한으로 관리자 승인이 필요할 수 있습니다.

두 ID는 공개 식별자이며 비밀번호가 아닙니다. 각 기기에서 연결 설정에 입력하거나 `config.js`에 ID만 기입하면 기기별 설정을 줄일 수 있습니다.

## 기존 일기 옮기기

기존 HTML에서 **백업**을 누르거나, 전달받은 `하루툰_기존기록_2026-10-04_06.json`을 사용합니다. 이 파일에는 개인 그림과 글이 포함되니 공개 저장소에 올리지 않습니다.

- OneDrive로 옮길 때: 먼저 **학교 OneDrive 연결** → **일기 가져오기** → 백업 JSON 선택.
- 브라우저 안에 보관할 때: OneDrive 연결 없이 **일기 가져오기**.
- 다른 기기: 홈페이지를 열고 같은 앱 ID와 학교 계정으로 로그인하면 같은 일기를 불러옵니다.

기존 날짜는 가져오기에서 건너뜁니다. 가져오는 중 실패하면 다시 같은 파일을 가져오면 남은 날짜를 진행합니다. 그림은 긴 변 1600px, JPEG로 압축합니다.

## 저장·동기화 동작

- 연결 전: IndexedDB에 저장합니다. 브라우저 데이터 삭제 시 사라질 수 있으므로 백업해주세요.
- 연결 후: 날짜별 JSON과 별도 그림 파일을 OneDrive에 저장합니다. HTML 크기는 일기 수에 따라 늘어나지 않습니다.
- 다른 기기 변경을 볼 때: **새로고침**. 실시간 공동 편집은 아닙니다.
- 동시 수정: OneDrive ETag로 변경 충돌을 감지하고 덮어쓰기를 막습니다. 충돌 시 작성 내용을 복사해 두고 취소 → 새로고침 → 다시 수정합니다.
- 그림을 교체하거나 일기를 삭제해도 이전 그림 파일은 OneDrive에 남습니다. 실패한 업로드의 그림도 남을 수 있습니다. 삭제 실수 방지를 위해 자동 정리는 하지 않습니다.
- 로그아웃은 앱의 인증 정보를 이 탭에서 지우고 브라우저 로컬 일기로 돌아갑니다. Microsoft 전체 계정을 로그아웃하는 기능은 아닙니다.
- 원격 로그인 토큰은 이 탭의 sessionStorage에 보관합니다. 로그인은 OAuth 2.0 PKCE 방식이며, 외부 JS 라이브러리에 의존하지 않습니다.
- 백업 JSON에는 저장된 모든 그림과 글이 들어갑니다. 작성 중인 미저장 내용은 포함되지 않습니다. 기록이 많으면 백업 파일도 커집니다.

## 검증 범위

JavaScript 문법 검사, 모의 IndexedDB의 저장·읽기·삭제, OneDrive API 모의 응답의 저장·충돌 방지, OAuth state 불일치 거부를 확인했습니다. 실제 브라우저 화면과 실제 학교 계정 연결은 아직 검증되지 않았습니다. 실제 학교 계정 로그인·권한 승인과 실제 OneDrive 업로드는 앱 등록 완료 후 확인해야 합니다.

공식 참고: [GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site), [Microsoft OAuth PKCE](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow), [Graph 권한](https://learn.microsoft.com/en-us/graph/permissions-reference), [업로드 세션](https://learn.microsoft.com/en-us/graph/api/driveitem-createuploadsession).

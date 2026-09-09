# 부광고ON (이동수업 앱)

Expo(React Native Web)로 빌드된 정적 웹앱입니다. 백엔드는 Supabase를 사용합니다.
Netlify 대신 **GitHub Pages**에서 서비스하기 위한 배포용 저장소입니다.

## 이 저장소에 들어있는 것

| 파일 | 설명 |
| --- | --- |
| `index.html` | 앱 진입점. 자산 경로를 모두 상대경로(`./`)로 지정 |
| `_expo/static/js/web/index-*.js` | 실제 앱이 들어있는 웹 번들 (약 740KB) |
| `_expo/static/js/ios/*.hbc`, `android/*.hbc` | 모바일 앱용 번들. 웹 서비스에는 쓰이지 않음 |
| `manifest.json`, `icon-512.png`, `favicon.png` | PWA(홈 화면 추가)용 설정과 아이콘 |
| `404.html` | 잘못된 주소로 들어왔을 때 앱 첫 화면으로 되돌림 |
| `.nojekyll` | **필수.** 없으면 `_expo` 폴더가 통째로 무시되어 앱이 안 뜸 |

## 배포 방법 (GitHub Pages)

1. GitHub에서 새 저장소를 만듭니다. (예: `movingclass`, Public)
2. 이 폴더 안의 **모든 파일을 저장소 최상위에** 올립니다.
   - 웹에서 올릴 때는 `movingclass-github` 폴더 자체가 아니라 **폴더 안의 내용물**을 올려야 합니다.
   - `.nojekyll` 은 이름이 점으로 시작해 탐색기에서 숨겨질 수 있으니 함께 올라갔는지 꼭 확인하세요.
3. 저장소 **Settings → Pages** 로 이동
   - Source: `Deploy from a branch`
   - Branch: `main` / 폴더: `/ (root)` → Save
4. 1~2분 뒤 `https://<깃허브아이디>.github.io/movingclass/` 에서 열립니다.

저장소 이름을 `movingclass`가 아닌 다른 이름으로 만들었다면 `404.html` 안의 `REPO` 값만 그 이름으로 바꿔주세요.

## Supabase 설정에서 같이 바꿔야 할 것

앱이 붙는 Supabase 프로젝트: `sskfshlefbhniiqmdydd`

- **Authentication → URL Configuration**
  - Site URL: `https://<깃허브아이디>.github.io/movingclass/`
  - Redirect URLs 에도 위 주소 추가
  - Netlify 주소는 완전히 정리한 뒤에 지우세요.
- 데이터 조회(REST)는 별도 CORS 설정 없이 어느 도메인에서든 동작하므로, 표 데이터는 그대로 잘 나옵니다.
- 앱이 사용하는 테이블: `attendance`, `chats`, `classes`, `classrequests`, `settings`, `students`, `teachers`

## 보안 참고

번들 안에 Supabase 주소와 **publishable key**가 들어 있습니다. 이 키는 원래 공개되는 것이 정상이며,
실제 데이터 보호는 각 테이블의 **RLS(Row Level Security) 정책**이 담당합니다.
저장소를 Public으로 만들기 전에 위 7개 테이블에 RLS가 켜져 있고 정책이 올바른지 한 번 확인하는 것을 권합니다.

## 앱 내용을 수정하려면

이 저장소에 있는 `index-*.js` 는 **컴파일·압축된 결과물**이라 직접 고칠 수 없습니다.
화면이나 기능을 바꾸려면 원본 Expo 프로젝트 소스가 필요합니다.
소스를 확보한 뒤 `npx expo export --platform web` 으로 다시 빌드하고,
새로 나온 `dist` 폴더의 내용으로 이 저장소를 덮어쓰면 됩니다.
(그때도 `.nojekyll` 과 상대경로 수정은 그대로 유지해야 합니다.)

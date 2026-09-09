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

## Supabase 설정에서 할 일

앱이 붙는 Supabase 프로젝트: `sskfshlefbhniiqmdydd`

**도메인을 옮기면서 Supabase에서 바꿀 설정은 없습니다.**

- 이 앱은 Supabase Auth를 사용하지 않습니다. 학번·이름·비밀번호로 `students` / `teachers`
  테이블을 직접 조회하는 자체 로그인 방식이라, Auth의 Site URL / Redirect URLs 설정과 무관합니다.
- 데이터 조회(REST)는 도메인 제한이 없어 새 주소에서 그대로 동작합니다.
  (GitHub Pages 주소에서 7개 테이블 전부 정상 응답 확인)
- 실시간 갱신(Realtime, `postgres_changes`)도 도메인별 설정이 없어 그대로 동작합니다.

앱이 사용하는 테이블: `attendance`, `chats`, `classes`, `classrequests`, `settings`, `students`, `teachers`

## ⚠️ 보안 점검 필요 (도메인 이전과 무관하게 원래부터 있던 사항)

번들에 들어 있는 Supabase publishable key는 공개되는 것이 정상이며, 실제 데이터 보호는
각 테이블의 **RLS(Row Level Security) 정책**이 담당합니다.

그런데 현재 이 키만으로 로그인 없이 아래 테이블 전체를 읽을 수 있는 상태입니다:

| 테이블 | 익명 읽기 | 행 수 |
| --- | --- | --- |
| `students` | 가능 | 155 |
| `teachers` | 가능 | 2 |
| `attendance` | 가능 | 2022 |
| `chats` | 가능 | 49 |
| `classes` | 가능 | 6 |
| `settings` | 가능 | 1 |

즉 주소만 아는 사람은 누구나 학생 155명의 명부와 출결 기록을 조회할 수 있습니다.
Netlify에서도 동일했던 상태라 이번 이전으로 새로 생긴 문제는 아니지만,
학생 개인정보가 걸려 있으므로 Supabase 대시보드에서 각 테이블의 RLS 정책을 점검하시기를 권합니다.

## 앱 내용을 수정하려면

이 저장소에 있는 `index-*.js` 는 **컴파일·압축된 결과물**이라 직접 고칠 수 없습니다.
화면이나 기능을 바꾸려면 원본 Expo 프로젝트 소스가 필요합니다.
소스를 확보한 뒤 `npx expo export --platform web` 으로 다시 빌드하고,
새로 나온 `dist` 폴더의 내용으로 이 저장소를 덮어쓰면 됩니다.
(그때도 `.nojekyll` 과 상대경로 수정은 그대로 유지해야 합니다.)

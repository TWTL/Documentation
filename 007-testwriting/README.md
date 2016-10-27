# Writing Test

TWTL Doc 007

프로젝트의 테스트 작성법에 대한 문서입니다.

## 테스트에 대하여

WIP

## TestSuite 저장소 관리

### 작업 순서

1. 공용 저장소(GitHub)의 main branch는 `master`입니다.
2. 작업용 branch는 local에서 관리합니다. 필요할 경우 remote로 push해 놓습니다.
3. 새 테스트를 작성해야 하는 경우, 작업용 branch를 하나 만들어서 진행합니다. **master에서 작업하지 않습니다.**
4. 작업용 branch에서 가장 먼저 해야 할 일은 README.md에 항목을 추가하는 것입니다. 테스트에 번호와 이름을 붙이고 README.md를 수정합니다.
5. remote의 master branch를 pull (fetch and merge) 한 후, 최대한 빨리 local에서 master branch에 작업용 branch를 merge한 후, push합니다. 테스트 번호와 이름이 중복되지 않도록 하는 작업입니다.
6. 작업용 branch와 master 간에 merge가 필요한 경우 `--no-ff` 옵션을 꼭 사용합니다.

### 개별 테스트 폴더 내용

`bin` 폴더와 `src` 폴더 내에 각각 빌드한 바이너리와 소스코드(또는 프로젝트 파일들)를 넣습니다.

## 테스트 바이너리 주의사항

* Visual Studio로 컴파일하는 경우 `vcruntime140.dll`(Release)/`vcruntime140d.dll`(Debug) (Runtime Redistributable)에 대한 의존성이 생기게 됩니다. 의존성을 없앱시다.

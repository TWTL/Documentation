# Installer

TWTL Doc 013

TWTL의 소프트웨어 패키지 설치 프로그램에 대해 설명합니다.

## Overview

설치 프로그램은 많은 소프트웨어 시스템에서 중요한 요소입니다. 최종 사용자에게 제공될 보안 솔루션으로 TWTL은 설치 프로그램의 형태로 제공됩니다.

## How to build an installer

### Prerequisite

* 빌드 시스템에 Git 저장소 사본 받기. TWTL/Build 저장소와 모든 서브모듈을 받습니다. 필요한 명령어는 'Getting a full clone' 부분을 참조하십시오.
* 빌드 시스템에 NSIS 3.0을 설치하기. MUI2를 사용하기 때문에 표준 플러그인도 모두 함께 설치합니다.

### Building binaries from each project

#### Engine

TWTL/Engine 저장소(서브모듈 Engine 폴더)의 TWTL_Engine은 Visual Studio 2015 Community 솔루션으로, C++ 프로젝트인 TWTL_Database, TWTL_Main, TWTL_Snapshot 총 3개를 포함하고 있습니다. 이들은 각각 TWTL_Database.dll, TWTL_Main.exe, TWTL_Snapshot.dll 파일을 생성합니다.

모든 파일이 확실히 생성되도록 하기 위해, TWTL_Database, TWTL_Main, TWTL_Snapshot 순서로 직접 빌드합니다. 모두 Release x86 옵션으로 빌드합니다.

#### GUI

TWTL/GUI 저장소(서브모듈 GUI 폴더)의 TWTL_UnityGUI는 Unity 5.4.3 프로젝트입니다. MainScene.unity를 열어 PC standalone으로 Windows x86 옵션을 줘 빌드합니다.

(예시 파일명/폴더명:) gui.exe 파일과 gui_Data 폴더가 생성되도록 합니다.

#### Service

TWTL/Service 저장소(서브모듈 Service 폴더)의 TWTLService는 Visual Studio 2015 Community 솔루션으로, C++ 프로젝트인 TWTLService를 포함하고 있습니다.

Release x86 옵션으로 빌드해서 TWTLService.exe를 생성합니다.

### Using NSIS 3.0

NSIS 3.0의 Compile NSI scripts (makensisw) 프로그램에서 본 저장소의 TWTL.nsi 스크립트를 열면 스크립트를 컴파일할 수 있습니다. 스크립트가 열린 창에서 'Script \> Recompile (Ctrl+R)'을 선택하거나, 윈도우 탐색기에서 TWTL.nsi 스크립트 파일을 우클릭하고 'Compile NSIS Script'를 선택해도 컴파일을 수행할 수 있습니다.

### About TWTL.nsi script

TWTL.nsi는 TWTLInstaller.exe를 생성하는 스크립트입니다. TWTL.defs.Files.nsh와 TWTL.macro.RemoveFilesAndSubDirs.nsh를 포함하며, 이 중 TWTL.defs.Files.nsh는 설치 파일을 빌드하는 데 필요한 파일들의 파일 경로에 대한 정의를 포함합니다. 각 프로젝트를 빌드한 후, 설치 파일이 생성되려면 이들 `!define BUILD_..._PATH` 항목들이 올바른 파일명 경로를 가리키도록 수정해야 합니다.

### Installation

설치 프로그램은 다음과 같이 구성됩니다.

* 파일 설치. 프로그램 파일과 제거 프로그램 (uninstaller).
* 레지스트리 설치. 프로그램에서 참조하는 값과 프로그램 추가/제거 정보.
* 서비스 설치 및 시작.

### Uninstallation

제거 프로그램은 다음과 같이 구성됩니다.

* 서비스 중지 및 제거.
* 파일 제거. 프로그램 파일과 제거 프로그램 자신.
* 레지스트리 제거.

## Maintaining repository

TWTL/Build 저장소는 개발용 저장소가 아닌 빌드 전용 저장소로, 원활한 진행을 위해 다음 노하우가 필요할 수 있습니다.

### Getting a full clone

1. [TWTL/Build](https://github.com/TWTL/Build) 저장소를 `git clone`으로 받습니다.
2. 저장소 루트에서 `git submodule init` 명령을 실행합니다.
3. 저장소 루트에서 `git submodule update` 명령을 실행합니다.

Git Submodule 기능에 대해서는 Git 사용자 매뉴얼을 참조하십시오.

이 명령은 제출용 소스 코드 사본을 만들 때에도 사용할 수 있으며 `clone`, `submodule update` 명령에는 `--depth=1` 옵션을 쓸 수 있습니다.

### Changing revision of submodule

저장소 루트에서 `git pull`, `git checkout`, `git merge` 등의 현재 루트 저장소의 리비전을 바꾸는 명령어를 사용하면 서브모듈의 리비전 참조 역시 바뀔 수 있습니다. 그러나 `git checkout`을 이용해 서브모듈을 외부에서 체크아웃해도 서브모듈 내부의 파일을 바꿀 수 없습니다.

서브모듈 외부에서 `git diff`를 이용해 서브모듈의 리비전을 알아낸 후, 서브모듈 내부에서 `git checkout`으로 내부 리비전을 바꾸고 내부 파일을 업데이트합니다. `git checkout`으로 로컬 브랜치 `master`를 체크아웃해 놓으면, 트래킹 브랜치 `origin/master`를 `git merge --ff`하는 것도 가능합니다. 마찬가지로 `master`를 체크아웃한 경우 서브모듈 내부에서 `git pull`로 원격지 저장소의 내용을 받아 올 수 있습니다.

서브모듈 내부에서 체크아웃을 해서 리비전 참조가 바뀌면 서브모듈 밖에서 이를 `git add` 할 수 있습니다.

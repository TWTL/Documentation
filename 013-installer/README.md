# Installer

TWTL Doc 013

TWTL의 소프트웨어 패키지 설치 프로그램에 대해 설명합니다.

## Overview

설치 프로그램은 많은 소프트웨어 시스템에서 중요한 요소입니다. 최종 사용자에게 제공될 보안 솔루션으로 TWTL은 설치 프로그램의 형태로 제공됩니다.

## How to build an installer

### Prerequisite: Get a copy of Git repo

1. [TWTL/Build](https://github.com/TWTL/Build) 저장소를 `git clone`으로 받습니다.
2. 저장소 루트에서 `git submodule init` 명령을 실행합니다.
3. 저장소 루트에서 `git submodule update` 명령을 실행합니다.

Git Submodule 기능에 대해서는 Git 사용자 매뉴얼을 참조하십시오.

이 명령은 제출용 소스 코드 사본을 만들 때에도 사용할 수 있으며 `clone`, `submodule update` 명령에는 `--depth=1` 옵션을 쓸 수 있습니다.

### Prerequisite: Get NSIS 3.0

빌드 시스템에 NSIS 3.0을 설치합니다.

### Building binaries from each project

TBD.

### Using NSIS 3.0

TBD.

### About TWTL.nsi script

TBD.

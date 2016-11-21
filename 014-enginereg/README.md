# Engine Feature: Registry

TWTL Doc 014

엔진의 레지스트리 감시 및 제어 기능에 대해 기술합니다.

## Overview

윈도우 레지스트리는 시스템의 모든 소프트웨어가 공유하는 데이터베이스입니다. 키-값으로 이루어진 구조는 파일시스템의 폴더-파일 구조와 유사하며, 권한을 가진 누구든 수정할 수 있다는 것이 특징입니다.

응용 프로그램뿐만 아니라 윈도우 운영체제의 커널과 시스템 서비스들이 레지스트리에서 값을 읽고 쓰는 구조는 윈도우 기반 컴퓨터 시스템에 축복인 동시에 재앙으로 작용해 왔습니다. 이런 역사적 이유로 최종 사용자를 위한 보안 솔루션에 레지스트리 감시와 제어는 빠뜨릴 수 없는 기능입니다. TWTL 엔진의 레지스트리 기능에 대해 정의합니다.

## 방식

가장 기본적인 방식은 대상이 되는 레지스트리 키와 값의 스냅샷을 꾸준히 생성해서 기존 스냅샷과 비교하는 것입니다. 현재 TWTL은 이 방식을 채택하고 있습니다. 대부분의 맬웨어가 실질적으로 시스템에 유해한 변경을 가하는 레지스트리 키는 정해져 있으며 추가 공격이 발생하거나 재부팅이 발생하기 전에 이를 되돌려 놓는 것이 좋습니다.

다음으로 RegOpenKey, RegQueryKey, RegSetInfoKey, RegSetValue, RegQueryKey 등의 레지스트리 관련 API에 hook을 해서 필터에 따라 위험할 수 있는 변경을 미리 차단하는 방법이 있습니다. 후킹으로 인한 오버헤드로 인해 현재 TWTL에서는 채택하지 못하고 있는 방식입니다.

## 대상

### 시작 프로그램 및 서비스

시스템 시작 시에 실행되는 프로그램에 대한 정보 항목입니다. GlobalServices에 등록된 것은 서비스로 실행되고, 나머지는 일반 프로세스로 실행됩니다.

* GlobalServices: `HKLM\System\CurrentControlSet\Services`
* GlobalRun: `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
* GlobalRunOnce: `HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce`
* UserRun: `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`
* UserRunOnce: `HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce`

이 값들은 엔진에서 모두 Name과 Value의 목록으로 관리됩니다. Name은 임의로 붙은 식별 가능한 이름이고, Value는 실행파일의 경로입니다. GlobalServices의 경우 Services 키 하위에 키들이 존재하며 이 키의 이름이 Name에, 키 내부 문자열 값 ImagePath의 데이터가 Value에 대응합니다. GlobalRun, GlobalRunOnce, UserRun, UserRunOnce의 경우 각 키 하위에 문자열 값들이 존재하며 값의 이름은 Name에, 값의 데이터는 Value에 대응합니다.

### 탐색기

TBD

### 인터넷

TBD

### 하드웨어 정보

TBD

### 운영체제 정보

TBD

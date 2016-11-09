# List of Variables

TWTL Doc 009

엔진 내부에서 관리되는 변수입니다.

## Overview

변수 리스트는 트리의 구조를 가지고 있으며, terminal node는 primitive value를 가집니다. 사용되는 primitive value의 종류는 다음과 같습니다.

* Integer32
* Uint32
* Float32
* String
* Object(...)

각 변수들에 대한 접근권한은 read, write, read&write로 다음과 같이 표현합니다.

* r
* w
* rw

## Listing

### /Engine/

#### /Engine/Name/

* Type: String
* Access: r

기본값: `"TWTL"`

엔진의 이름을 나타낸다.

#### /Engine/Version/

* Type: String
* Access: r

기본값: `"1.0"`

엔진의 버전을 나타낸다.

#### /Engine/RequestPort/

* Type: Integer32
    * 추가 제약사항: 이 값은 valid TCP port number (1~65535) 이어야 한다.
* Access: r

기본값: `5259`

엔진이 GUI로부터 메시지 요청을 받고 응답을 하는 주 포트 번호이다.

#### /Engine/TrapPort/

* Type: Integer32
    * 추가 제약사항: 이 값은 valid TCP port number (1~65535) 이어야 한다.
* Access: rw

기본값: 정의되지 않음. 이 값은 읽히기 전에 우선 쓰여야 한다. 쓰이기 전에 읽힐 경우 유효한 값이 아닐 수 있다.

엔진이 GUI의 요청 없이 GUI에 메시지를 보내는 포트이다. GUI가 켜질 때 임의로 포트를 열어서 Listen하고, 엔진에 이를 알려줘야 한다.

#### /Engine/PortsListening/
* Type: Boolean
* Access: rw
기본값: 'FALSE'

#### /Engine/Log/
* Type: Object(...)
* Access: rw
기본값: 정의되지 않음.


### /Net/

### /Perf/

### /Files/

#### /Files/


### /Reg/

#### /Reg/Name/
* Type: String
* Access: r

#### /Reg/Value/
* Type: String
* Access: r

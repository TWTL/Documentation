# Engine Variables

TWTL Doc 009

엔진 내부에서 관리되는 변수에 관한 설명과 그 목록입니다.

## Overview

### Structure and types

변수 리스트는 트리의 구조를 가지고 있으며, 트리이기 때문에 terminal node와 non-terminal node로 구성됩니다.

모든 terminal node는 원시 값(primitive value)을 가집니다. 사용되는 원시 값의 종류는 다음과 같습니다.

* Integer32
* Uint32
* Float32
* String
* Boolean

모든 non-terminal node는 내부에 여러 값이 있는 복합 값(complex value)을 갖습니다. 복합 값은 (1) 서로 다른 이름이 붙은 여러 타입의 값들의 순서 없는 모음이거나, (2) 개별적으로 이름이 붙지 않는 일정한 타입의 값들의 순서 있는 모음입니다. 전자를 Object, 후자를 List라 합니다.

* Object(...)
* List\<...\>

Object 타입의 값은 하위 노드의 이름을 붙여 나타내며, 하위 노드에 대해 따로 기술합니다. 한편 List 타입은 내부 타입의 이름을 붙여 나타냅니다. 예를 들면 다음과 같습니다.

* 이름이 Foo, Bar, Baz인 키를 갖는 Object: Object(Foo, Bar, Baz)
* 타입이 SomeType인 값을 갖는 List: List\<SomeType\>

어떤 node에든 임의로 접근할 수 있으며 terminal node를 통해 접근해야만 하는 것은 아닙니다. 다만 개발 단계에 따라 실제로 접근 가능하도록 구현되는 노드는 제한적일 수 있습니다.

### Access previliges

각 변수들에 대한 접근권한은 read, write, read&write로 다음과 같이 표현합니다.

* r
* w
* rw

복합 값을 갖는 어떤 변수는 하위 변수들의 접근권한이 일정하지 않을 경우 하나의 접근권한을 갖지 않을 수 있습니다. 이 경우 접근권한은 `-`로 표현합니다.

## Full listing

* Path: `/`
* Type: Object(Engine, Net, Perf, Files, Reg)
* Access: -

### /Engine/

* Type: Object(Name, Version, RequestPort, TrapPort, PortsListening, PortsConnecting, Log)
* Access: -

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
* Access: r

기본값: `FALSE`

#### /Engine/PortsConnecting/
* Type: Boolean
* Access: rw

기본값: `FALSE`

#### /Engine/Log/
* Type: List\<String\>
* Access: rw

기본값: 정의되지 않음.

### /Net/

* Type: Object(ConnectingList)

#### /Net/ConnectingList/
* Type: List\<String\>
* Access: rw

기본값: 정의되지 않음.

### /Perf/

### /Files/

### /Reg/

#### /Reg/Key/
* Type: Object(...)
* Access: r

#### /Reg/Key/Name/
* Type: String
* Access: r

#### /Reg/Key/Value/
* Type: String
* Access: r

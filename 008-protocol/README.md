# Protocol

TWTL Doc 008

전체 구조에서 엔진-프로토콜 통신에 필요한 규약을 기술합니다.

## 개요

[TWTL Doc 006](/006-architecture)에서 다루었듯, TWTL 시스템의 핵심 기능은 Engine에서 담당하며, 사용자는 GUI를 통해 엔진의 동작을 제어하며 필요한 정보를 얻게 됩니다.

프로젝트의 타깃 플랫폼인 Win32 시스템에서 Engine과 GUI는 별도의 프로세스로 동작하도록 설계되었습니다. 따라서 둘 사이에 통신이 이루어질 방법이 필요하며, IP 스택 상에서 TCP/UDP 소켓 통신을 이용하는 것으로 정하였습니다.

이 문서에서 기술하는 프로토콜은 Engine과 GUI가 TCP/UDP 소켓을 이용할 방법에 대해 포괄적으로 다룹니다. 구체적인 내용으로는 연결의 의미론, 메시지의 구조, 연결 양단이 취해야 할 상태 등이 있습니다.

## 구조

![Structure](./Protocol-Structure.png)

### Request, Response

항상 실행 중인 상태를 취해야 하는 Engine은, 사용자가 임의로 껐다 켤 수 있어야 하는 GUI에 비해 안정적이어야 합니다. 이런 맥락과 플랫폼 안정성을 고려한 구조로, 주 채널은 Engine이 Listen하면 GUI가 Connect함으로써 성립합니다. Engine의 TCP Listen 포트 번호는 다음과 같습니다.

* 주 포트: 5259
* 보조 포트: 15259 (현재 사용하지 않음)

주 채널에서 Engine과 GUI의 연결은 일대일입니다. 이 채널에서 사용자가 GUI를 통해 얻어야 하는 대부분의 정보를 요청하고 또한 Engine에 특정한 동작을 하도록 명령을 내리게 됩니다. GUI가 먼저 메시지를 보내면, Engine은 받은 메시지에 대응하는 메시지로 응답합니다. GUI가 먼저 보내는 메시지를 Request, Engine이 나중에 보내는 메시지를 Response라 합니다.

각 메시지는 TCP를 통해 하나의 시퀀스로 전달되며, 시퀀스의 끝은 메시지의 끝을 의미합니다. Request와 Response는 각각 하나의 메시지입니다. 따라서 GUI가 보내는 Request 메시지를 담은 한 세그먼트 시퀀스가 끝나면, Engine은 그 메시지를 처리해 한 Response 메시지를 보내고 다시 Request 메시지를 받을 준비를 합니다. Engine은 먼저 연결을 끊어서는 안 됩니다.

Response는 Request보다 먼저 올 수 없습니다. Engine은 GUI의 Request 메시지를 기다립니다. 이 때문에 Engine이 GUI에 먼저 메시지를 보내야 할 경우를 대비해 Trap, Trap-Ack을 위한 채널이 필요하게 됩니다. 이에 대해서는 다음 문단으로 이어집니다.

### Trap, Trap-Ack

어떤 정보는 GUI가 Request하기 전에 사용자에게 즉시 전달될 필요가 있습니다. GUI에서 짧은 시간 간격으로 많은 정보를 Request함으로써 이를 극복할 수 있지만, TWTL 시스템의 주요 기능 중 하나가 네트워크 모니터링이라는 점에서 경우에 따라 이는 좋은 방법이 아닐 수 있습니다. push와 polling은 본질적으로 다른 의미론이기에 때에 맞게 이를 활용할 수 있는 것이 좋습니다.

Trap과 Trap-Ack은 이를 위한 메시지 타입입니다. Request/Response와는 달리, Trap/Trap-Ack을 위한 연결은 GUI가 활성일 경우 GUI가 먼저 임의의 Port를 정해 Listen을 하고, Engine이 여기에 Connect하는 방식으로 수립됩니다.

## 메시지 형식

### 컨테이너

모든 메시지는 각각 JSON 형식을 따르며 유효한 JSON Object여야 합니다.

메시지 오브젝트는 다음 속성을 갖습니다.

* name (string)
* app (string)
* version (string)
* contents (array)

현행 구현체에서 `name`은 `"TWTL"`, `version`은 `"1"`을 각각 고정값으로 갖도록 하며, `app`은 메시지 송신자에 따라 `"TWTL-GUI"` 또는 `"TWTL-Engine"`입니다. `name`과 `version`은 메시지가 프로토콜을 준수하는지 검증하기 위한 최소한의 장치입니다.

`contents`는 일정한 구조를 갖는 오브젝트의 배열이며 배열의 항목들을 메시지 항목(message item)이라고 합니다. 메시지 항목은 메시지의 의미를 전달하는 최소 단위입니다. `contents`는 메시지 항목의 배열입니다.

모든 메시지 항목 오브젝트는 `type` 속성과 `path` 속성을 갖습니다. `path`는 문자열 값으로, 메시지 항목이 대상으로 하는 엔진 변수를 참조하는 경로사양입니다. 변수의 타입과 경로사양, 연산에 대해서는 선행 문서인 [TWTL Doc 009](/009-variables)를 참고하십시오.

메시지 항목 오브젝트의 `path` 속성이 대상을 지정하는 값이라면, `type` 속성은 그 대상에 대한 목적 작업을 지정하는 값입니다. `type`은 메시지 항목의 유형을 지정하는 값이며 유형에 따라 메시지 항목 오브젝트는 다른 속성 값을 갖기도 합니다. 메시지 항목 유형에 대한 설명으로 이어집니다.

### 메시지 항목 유형

메시지의 내용은 메시지의 용도에 따라 정해집니다. 메시지는 항상 다음 중 하나일 것입니다.

* Request
* Response
* Trap
* Trap-Ack

메시지 항목 오브젝트의 `type` 속성은 다음 중 하나의 값을 갖습니다.

* Request:
    * `"request.get"`
    * `"request.set"`
    * `"request.diff"`
    * `"request.patch"`
    * `"request.put"`
    * `"request.delete"`
    * `"request.beta"`
    * `"request.head"` (TBD)
    * `"request.alpha"` (TBD)
* Response:
    * `"response.status"`
    * `"request.object"`
* Trap:
    * `"trap.change"`
* Trap-Ack:
    * `"trap-ack.check"`

#### Request

Request 메시지 항목은 항상 하나의 엔진 변수에 대한 읽기 또는 쓰기를 수행하는 명령의 모음입니다. 대체로 하나의 Request 메시지는 하나의 항목을 포함하고 있을 것으로 간주됩니다. 그러나 본 규약에 의하면 한번에 여러 변수의 값을 가져오거나 보내거나 또는 동시에 적용할 수 있습니다.

메시지 항목 유형에 따른 용도, 추가 필드와 그 의미, 제약사항은 다음과 같습니다.

* `request.get`
    * 지정한 경로사양의 트리로부터 값을 읽기.
    * 이 유형의 메시지 항목에는 별도로 정의되는 필드가 없습니다.
    * 대부분의 노드는 읽을 수 있으며 권한만 있다면 (권한 r) 경로사양으로부터 값을 읽어 오는 것은 제약이 따르지 않는 일입니다.
* `request.set`
    * 지정한 경로사양의 트리에 값을 쓰기.
    * `value`: 새 값
    * 경로사양으로 지정한 노드는 쓸 수 있어야 하며 (권한 w), `value` 필드 값의 타입은 지정한 노드의 타입과 하위 트리 전체에서 같아야 합니다. 타입이 다르면 값이 쓰이지 않습니다.
* `request.put`
    * 지정한 경로사양을 갖도록 새 노드와 값을 넣기.
    * `value`: 새 값
    * 경로사양으로 지정한 노드는 생성 가능한 노드여야 하며 (권한 c), `value` 필드 값의 타입은 지정한 노드의 타입과 하위 트리 전체에서 같아야 합니다. 타입이 다르면 새 노드가 생성되지 않습니다.
* `request.delete`
    * 지정한 경로사양을 갖는 노드를 삭제하기
    * 이 유형에 별도의 필드 없음.
    * 경로사양으로 지정한 노드는 소멸 가능한 노드여야 합니다 (권한 d).
* `request.diff`
    * 지정한 경로사양의 트리의 값의 변경사항 읽기
    * 경로사양으로 지정한 노드가 중복과 순서가 없는 List\<T\> 타입의 노드인 경우
        * 경로사양으로 지정한 노드는 읽을 수 있는 값이어야 합니다.
* `request.patch`
    * 지정한 경로사양을 트리의 값에 변경사항 쓰기
    * 경로사양으로 지정한 노드가 중복과 순서가 없는 List\<T\> 타입의 노드인 경우
        * `value`: 지정한 노드에 변경사항을 반영할 원소 값의 리스트
        * `accept`: `value`의 각 변경사항 항목이 추가 대상임을 지정하는 리스트
        * `reject`: `value`의 각 변경사항 항목이 삭제 대상임을 지정하는 리스트
        * 경로사양으로 지정한 노드는 중복과 순서가 없는 List\<T\> 타입의 노드로 원소를 추가, 삭제할 수 있어야 합니다 (권한 w=ie). `value`는 List\<T\> 타입의 값이고, 모든 원소가 지정한 노드의 원소여야 합니다. `accept`와 `reject`는 모두 `value`와 같은 크기의 List\<Boolean\> 값이어야 합니다.
* `request.beta`
    * 지정한 경로사양의 함수에 조건값 쓰고 결과값 읽기
    * `value`: 함수 인수
    * 경로사양으로 지정한 노드는 함수 타입이어야 하고 적용 가능해야 하며 (권한 a), `value` 필드의 값의 타입은 노드의 입력 타입과 같아야 합니다.

한 Request 메시지에서 항목들은 `path` 필드에 대해 유일할 것으로 가정합니다. 한 Request 메시지 안에서 `path` 필드가 같은 여러 항목이 있는 경우 이는 정의되지 않은 동작을 초래합니다.

`diff`, `patch`는 `path` 필드에 경로사양으로 지정한 노드가 중복과 순서가 없는 List\<T\> 타입의 노드인 경우에만 정의되어 있습니다. 실제로는 순서와 중복이 있는 List\<T\> 타입의 노드나, 일반적인 Object 타입의 노드에도 정의가 가능합니다. TBD.

#### Response

Response 메시지의 메시지 항목은, Request 메시지의 각 항목에 대응하는 항목으로 이루어져 있습니다. Response 항목이 Request 항목에 대응한다는 것은 그 항목과 같은 `path` 값을 가짐을 의미합니다. 보다 구체적으로, Response 항목들은 대응하는 Request 항목이 기술한 목적 작업의 결과를 제시합니다.

메시지 항목 유형에 따른 용도, 추가 필드와 그 의미, 제약사항은 다음과 같습니다.

* `response.status`
    * 대응하는 Request 메시지 항목의 처리 상태.
    * `value`: Status code.
    * Request가 적법한 메시지 컨테이너 형식을 따르는 메시지인 경우, `path`가 지정된 모든 Request 메시지 항목에 대해 이 유형의 항목은 항상 Response 메시지에 포함되어야 한다. `type`이나 `path` 필드 값의 문제, 권한 등 선결 제약조건의 문제나 다른 필드의 문제는 Status code로 표현된다.
* `response.object`
    * 대응하는 Request 메시지 항목의 결과 값.
    * 대응하는 항목이 `request.set`, `request.put`, `request.delete`, `request.patch` 유형인 경우, 해당 작업은 결과 값을 요구하지 않으므로 이 메시지 항목은 없다.
    * 대응하는 항목이 `request.get`, `request.diff`, `request.beta` 유형인 경우 해당 작업은 결과 값을 요구하므로, Status code가 200이라면 (정상 처리 완료) 이 메시지 항목이 존재해야 한다.
    * 대응하는 항목이 `request.get`인 경우
        * `value`: `path`에 지정한 경로사양에 맞는 노드의 값.
    * 대응하는 항목이 `request.diff`인 경우
        * `path` 필드에 경로사양으로 지정한 노드가 중복과 순서가 없는 List\<T\> 타입의 노드인 경우
            * `value`: List\<T\> 타입 값으로, `path`에 지정한 경로사양에 맞는 List 노드에서 변경사항에 포함되는 모든 원소 값의 List.
            * `appear`: List\<Boolean\> 타입 값으로, `value`와 크기가 같으며, 원소는 해당 인덱스의 `value`의 원소가 새로 나타난 것임을 나타냄
            * `disapp`: List\<Boolean\> 타입 값으로, `value`와 크기가 같으며, 원소는 해당 인덱스의 `value`의 원소가 사라진 것임을 나타냄
    * 대응하는 항목이 `request.beta`인 경우
        * `value`: `path`에 지정한 경로사양에 맞는 함수 타입 노드의 출력 타입. 대응하는 항목의 `value` 입력값에 노드에 정의된 함수를 적용해 얻어 낸 결과값.

Request 메시지에 응답하는 Response 메시지는 모든 Request 메시지 항목에 대응하는 메시지 항목을 포함해야 하며(적어도 `response.status`는 항상 있음), 그 외의 메시지 항목을 포함하지 않습니다.

#### Trap과 Trap-ack

Trap과 Trap-ack의 메시지 항목 유형은 각각 한 가지씩만 정의되어 있습니다.

`"trap.change"`는 Trap 메시지 항목의 유형으로, `path`에 지정한 노드가 위험한 값으로 바뀌었음을 통지합니다. 별도의 필드는 없습니다.

`"trap-ack.check"`는 Trap-Ack 메시지 항목의 유형으로, 응답 대상인 Trap 메시지 내의 `"trap.change"` 메시지 항목에 대응합니다(즉, 같은 `path` 필드 값을 갖습니다). Engine의 Trap 메시지에 대해 GUI는 지목된 `path`와 관련된 루틴을 준비합니다. 이 메시지 유형에는 Boolean 타입의 `ready` 필드가 정의되어 있으며, 루틴을 찾으면 메시지 항목의 `ready` 필드를 `true`로, 루틴을 찾지 못하면 `false`로 설정합니다.

### `value` 필드

request.set과 response.object 항목의 `value` 필드는 `path` 필드의 문자열 값을 경로명으로 하는 변수의 값을 의미합니다. 이 필드의 값은 JSON에서 유효한 필드 값으로 어떤 것이든 유효합니다. 즉 string, number, object, array, boolean, null 중 어떤 값이든 올 수 있습니다.

구체적으로 이 값은 `path` 필드에 지정한 경로명의 변수 타입과 일치해야 합니다. 변수의 원시 타입은 [TWTL Doc 009](/009-variables)에 정의된 바와 같이 다음과 같습니다.

* Integer32, Uint32, Float32. 각각 number에 대응하며, ECMAScript Number 타입이 IEEE 754 binary64 형식으로 저장되고 처리될 것으로 규정되어 있기 때문에, 이 타입들 각각의 범위 내에 있는 값 모두를 Number 형식으로 정확히 나타낼 수 있습니다. Number 형식의 값이 지정된 변수 타입으로 나타낼 수 없는 범위나 정밀도를 요구하는 값이어서는 안 됩니다. 예를 들어 JSON에서 Integer32, Uint32로 지정된 값의 경우 소숫점 없이 표기되어야 하며 Uint32의 경우 부호 없이 표기되어야 합니다.
* String. string에 대응합니다. 이 값은 이스케이프 문자 `\"`, `\\`, `\/`, `\b`, `\f`, `\n`, `\r`, `\t`를 포함하거나, `\uXXXX`로 지정된 임의의 UTF-16 코드포인트를 포함할 수 있습니다. 참고로, UTF-16 서로게이트 시퀀스는 `\uXXXX` 코드포인트 두 개로 표현됩니다.
* Boolean. boolean에 대응합니다. TRUE, FALSE가 각각 `true`, `false`.
* Object. object에 대응하며 변수의 정의에 따라 하위 변수의 이름을 필드로 갖고 하위 변수의 값을 그 필드의 속성값으로 갖는 오브젝트 값입니다.
* List. array에 대응하며 변수의 정의에 따라 이 배열이 포함하는 값들은 일정한 타입 또는 일정한 스키마를 갖습니다.
* Opt<T>. T 타입의 가능한 값들 또는 `null`이 올 수 있습니다.
* Func<T>. 노드의 실제 값을 직접 읽고 쓸 수 없습니다.

### Status code

response.status 항목의 value는 변수가 아닙니다. 이 값은 명령의 처리 결과가 성공인지 실패인지, 실패라면 원인은 무엇인지를 나타내는 Status code입니다. Status code는 Number 자료형으로, 값은 다음 중 하나이며 뜻은 다음과 같습니다.

* 100: 지금은 곤란하다 잠시만 기다려 달라.
* 200: 네가 시킨 대로 했다.
* 300: 여기로 가면 원하는 걸 할 수 있다.
* 400: 네가 잘못했다.
* 500: 내가 잘못했다.

## 시나리오

## 예시

### 예시: \<init\>, 상황 1

이 예시에서는 `<init>` 시나리오에서 GUI-Engine 사이에서 교환될 JSON 예시 메시지를 모두 나열했습니다.

이 구역의 본문은 [Example-init-1.md](./Example-init-1.md)입니다.

### 예시: \<init\>, 상황 2

이 예시에서는 `<init>` 시나리오에서 GUI가 보낼 모든 메시지 항목을 하나의 Request 메시지에 보내는 경우를 가정하였습니다. 이 경우 Engine 역시 하나의 Response 메시지로 응답하게 됩니다.

이 구역의 본문은 [Example-init-2.md](./Example-init-2.md)입니다.

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

메시지는 다음 property를 가집니다.

* name (string)
* app (string)
* version (string)
* contents (array)

현행 구현체에서 `name`은 `"TWTL"`, `version`은 `"1"`을 각각 고정값으로 갖도록 하며, `app`은 메시지 송신자에 따라 `"TWTL-GUI"` 또는 `"TWTL-Engine"`입니다. 이 부분은 메시지가 프로토콜을 준수하는지 검증하기 위한 최소한의 장치입니다.

`contents`는 오브젝트의 배열로, 각 오브젝트는 `type`과 `path` 속성을 갖습니다. 배열의 내용물을 메시지 항목(item)이라고 합니다.

### 메시지 내용

메시지의 내용은 메시지의 용도에 따라 정해집니다. 메시지는 항상 다음 중 하나일 것입니다.

* Request
* Response
* Trap
* Trap-Ack

각각의 경우에 대해 `contents` 배열 내에는 다음 `type`을 갖는 메시지 항목이 있게 됩니다.

* Request: 다음 중 하나
    * `"request.get"`
    * `"request.set"`
    * `"request.head"` (TBD)
    * `"request.diff"` (TBD)
    * `"request.patch"` (TBD)
    * `"request.append"` (TBD)
* Response:
    * `"request.get"`에 대한 응답으로, `"response.status"`와 `"request.object"`
    * `"request.set"`에 대한 응답으로, `"response.status"`
* Trap: TBD
* Trap-Ack: TBD

#### Request

Request 메시지는 항상 하나 이상의 엔진 변수([TWTL Doc 009](/009-variables) 참조)에 대한 get 또는 set 명령의 모음입니다. 변수의 값을 엔진으로부터 가져오는 경우 `"request.get"`, 엔진에 변수의 값을 설정하는 경우 `"request.set"` 타입의 메시지 항목이 들어있게 됩니다.

대체로 하나의 Request 메시지는 하나의 명령 항목을 포함하고 있을 것으로 간주되지만, 본 규약에 의하면 한번에 여러 변수의 값을 가져오거나 보내거나 또는 동시에 가져오고 보낼 수 있습니다.

request.get 타입의 항목은 `path` 속성을 가지며, request.set 타입의 항목은 `path`와 `value` 속성을 갖습니다. 이는 request.get이 변수의 값을 취득하기 위한 명령이며, request.get이 변수의 값을 설정하기 위한 명령이기 때문입니다.

#### Response

Response 메시지는 Request 메시지의 각 명령에 대응하는 결과 항목으로 이루어져 있습니다. Request 메시지는 항상 대응하는 Response 메시지의 각 메시지 항목에 대한 `"response.status"` 타입의 메시지 항목들을 갖습니다. 이는 즉 각 메시지 항목에서 명령의 대상이 되는 변수마다 명령의 결과를 반환한다는 것입니다. 즉 `/.../A` 변수에 대한 명령 항목과 `/.../B` 변수에 대한 명령 항목을 담은 Request 메시지에 대응하는 Response 메시지는 `/.../A` 변수에 대한 명령의 결과 항목과 `/.../B` 변수에 대한 명령의 결과 항목을 포함해야 합니다.

request.get 타입의 항목에 대한 응답은 해당 변수에 대응하는 `"response.status"`와 더불어 `"response.object"` 타입의 항목을 수반하게 됩니다. request.set 타입의 항목은 해당 변수에 대응하는 `"response.status"` 항목만을 수반합니다. 이는 request.get이 변수의 값을 취득하기 위한 명령이기 때문에 그 결과로 변수의 값을 돌려 주는 것입니다. request.set의 경우 변수의 값을 설정하기 위한 명령이 잘 적용되었는지 결과를 돌려주는 것으로 충분합니다.

response.status 타입의 항목과 response.object 타입의 항목은 모두 `path`와 `value` 속성을 갖습니다. 다만 response.status 항목의 value가 200이 아닌 경우 response.object 항목이 아예 없는 것이 정상입니다.

### Object value

request.set과 response.object 항목의 `value` 필드는 `path` 필드의 문자열 값을 경로명으로 하는 변수의 값을 의미합니다. 이 필드의 값은 JSON에서 유효한 필드 값으로 어떤 것이든 유효합니다. 즉 string, number, object, array, boolean, null 중 어떤 값이든 올 수 있습니다.

구체적으로 이 값은 `path` 필드에 지정한 경로명의 변수 타입과 일치해야 합니다. 변수의 원시 타입은 [TWTL Doc 009](/009-variables)에 정의된 바와 같이 다음과 같습니다.

* Integer32, Uint32, Float32. 각각 number에 대응하며, ECMAScript Number 타입이 IEEE 754 binary64 형식으로 저장되로 처리될 것으로 규정되어 있기 때문에, 이 타입들 각각의 범위 내에 있는 값 모두를 정확히 나타낼 수 있습니다. Integer32, Uint32로 지정된 값의 경우 소수점 없이 표기되어야 합니다.
* String. string에 대응합니다. 이 값은 이스케이프 문자 `\"`, `\\`, `\/`, `\b`, `\f`, `\n`, `\r`, `\t`를 포함하거나, `\uXXXX`로 지정된 임의의 UTF-16 코드포인트를 포함할 수 있습니다. 참고로, UTF-16 서로게이트 시퀀스는 `\uXXXX` 코드포인트 두 개로 표현됩니다.
* Boolean. boolean(필드 값 `true`, `false`)에 대응합니다.
* Object. object에 대응하며 변수의 정의에 따라 하위 변수의 이름을 필드로 갖고 하위 변수의 값을 그 필드의 속성값으로 갖는 오브젝트 값입니다.
* List. array에 대응하며 변수의 정의에 따라 이 배열이 포함하는 값들은 일정한 타입 또는 일정한 스키마를 갖습니다.

null이 와도 되는 경우는 아직 없습니다.

### Status code

response.status 항목의 value는 변수가 아닙니다. 이 값은 명령의 처리 결과가 성공인지 실패인지, 실패라면 원인은 무엇인지를 나타내는 Status code입니다. Status code는 Number 자료형으로, 값은 다음 중 하나이며 뜻은 다음과 같습니다.

* 100: 지금은 곤란하다 잠시만 기다려 달라.
* 200: 네가 시킨 대로 했다.
* 300: 여기로 가면 원하는 걸 할 수 있다.
* 400: 네가 잘못했다.
* 500: 내가 잘못했다.

## 시나리오

## 예시

### 예시: <init>, 상황 1

이 예시에서는 `<init>` 시나리오에서 GUI-Engine 사이에서 교환될 JSON 예시 메시지를 모두 나열했습니다.

이 구역의 본문은 [Example-init-1.md](./Example-init-1.md)입니다.

### 예시: <init>, 상황 2

이 예시에서는 `<init>` 시나리오에서 GUI가 보낼 모든 메시지 항목을 하나의 Request 메시지에 보내는 경우를 가정하였습니다. 이 경우 Engine 역시 하나의 Response 메시지로 응답하게 됩니다.

이 구역의 본문은 [Example-init-2.md](./Example-init-2.md)입니다.

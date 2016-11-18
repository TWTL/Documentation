# Protocol

TWTL Doc 008

전체 구조에서 엔진-프로토콜 통신에 필요한 규약을 기술합니다.

## 개요

![Structure](./Protocol-Structure.png)

포트 번호는 다음과 같습니다.

* 주 포트: 5259
* 보조 포트: 15259

## 예시

아래 예시에서는 `<init>` 시나리오에서 GUI-Engine 사이에서 교환될 JSON 예시 메시지를 모두 나열했습니다.

GUI → Engine
````
{
	"name": "TWTL",
	"app": "TWTL-GUI",
	"version": "1",
	"contents": [
		{
			"type": "request.get",
			"name": "/Engine/Name/"
		}
	]
}
````

Engine → GUI
````
{
	"name": "TWTL",
	"app": "TWTL-Engine",
	"version": "1",
	"contents": [
		{
			"type": "response.status",
			"name": "/Engine/Name/",
			"value": 200
		},
		{
			"type": "response.object",
			"name": "/Engine/Name/",
			"value": "TWTL"
		}
	]
}
````

GUI → Engine
````
{
	"name": "TWTL",
	"app": "TWTL-GUI",
	"version": "1",
	"contents": [
		{
			"type": "request.get",
			"name": "/Engine/Version/"
		}
	]
}
````

Engine → GUI
````
{
	"name": "TWTL",
	"app": "TWTL-Engine",
	"version": "1",
	"contents": [
		{
			"type": "response.status",
			"name": "/Engine/Version/",
			"value": 200
		},
		{
			"type": "response.object",
			"name": "/Engine/Version/",
			"value": "1.0"
		}
	]
}
````

GUI → Engine
````
{
	"name": "TWTL",
	"app": "TWTL-GUI",
	"version": "1",
	"contents": [
		{
			"type": "request.get",
			"name": "/Engine/RequestPort/"
		}
	]
}
````

Engine → GUI
````
{
	"name": "TWTL",
	"app": "TWTL-Engine",
	"version": "1",
	"contents": [
		{
			"type": "response.status",
			"name": "/Engine/RequestPort/",
			"value": 200
		},
		{
			"type": "response.object",
			"name": "/Engine/RequestPort/",
			"value": 5259
		}
	]
}
````

GUI → Engine
````
{
	"name": "TWTL",
	"app": "TWTL-GUI",
	"version": "1",
	"contents": [
		{
			"type": "request.set",
			"name": "/Engine/TrapPort/",
			"value": 48207
		}
	]
}
````

Engine → GUI
````
{
	"name": "TWTL",
	"app": "TWTL-Engine",
	"version": "1",
	"contents": [
		{
			"type": "response.status",
			"name": "/Engine/TrapPort/",
			"value": 200
		}
	]
}
````

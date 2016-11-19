TWTL Doc 008 Protocol - 구역 본문

# 예시: <init>, 상황 2

이 예시에서는 `<init>` 시나리오에서 GUI가 보낼 모든 메시지 항목을 하나의 Request 메시지에 보내는 경우를 가정하였습니다. 이 경우 Engine 역시 하나의 Response 메시지로 응답하게 됩니다.

GUI → Engine
````
{
	"name": "TWTL",
	"app": "TWTL-GUI",
	"version": "1",
	"contents": [
		{
			"type": "request.get",
			"path": "/Engine/Name/"
		},
		{
			"type": "request.get",
			"path": "/Engine/Version/"
		},
		{
			"type": "request.get",
			"path": "/Engine/RequestPort/"
		},
		{
			"type": "request.set",
			"path": "/Engine/TrapPort/",
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
			"path": "/Engine/Name/",
			"value": 200
		},
		{
			"type": "response.object",
			"path": "/Engine/Name/",
			"value": "TWTL"
		},
		{
			"type": "response.status",
			"path": "/Engine/Version/",
			"value": 200
		},
		{
			"type": "response.object",
			"path": "/Engine/Version/",
			"value": "1.0"
		},
		{
			"type": "response.status",
			"path": "/Engine/RequestPort/",
			"value": 200
		},
		{
			"type": "response.object",
			"path": "/Engine/RequestPort/",
			"value": 5259
		},
		{
			"type": "response.status",
			"path": "/Engine/TrapPort/",
			"value": 200
		}
	]
}
````

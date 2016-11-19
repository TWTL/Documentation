TWTL Doc 008 Protocol - 구역 본문

# 예시: <init>

이 예시에서는 `<init>` 시나리오에서 GUI-Engine 사이에서 교환될 JSON 예시 메시지를 모두 나열했습니다.

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
			"path": "/Engine/Version/"
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
			"path": "/Engine/Version/",
			"value": 200
		},
		{
			"type": "response.object",
			"path": "/Engine/Version/",
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
			"path": "/Engine/RequestPort/"
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
			"path": "/Engine/RequestPort/",
			"value": 200
		},
		{
			"type": "response.object",
			"path": "/Engine/RequestPort/",
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
			"path": "/Engine/TrapPort/",
			"value": 200
		}
	]
}
````

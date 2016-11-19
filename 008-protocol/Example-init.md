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

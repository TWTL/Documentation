# Engine Database

TWTL Doc 010

엔진 내부에서 관리하는 데이터베이스에 대해 기술합니다.

## 개요

엔진 내부에서 쉽고 정확하게 많은 정보를 관리하기 위해 파일시스템에 의존적인 데이터베이스를 사용합니다. 스키마는 데이터베이스 시스템 고유의 데이터 모델을 따르며 엔진 내부의 데이터 모델과는 차이가 있을 수 있습니다. 이에 관해서는 엔진에서 구현해야 합니다.

엔진에서는 SQLite 3을 사용합니다.

## 스키마

### 프로세스 모니터링 (Perf)

````
CREATE TABLE IF NOT EXISTS
snapshot_process(
    idx INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL
    time TEXT NOT NULL
    pid INTEGER
    ppid INTEGER
    process_name TEXT NOT NULL
    process_path TEXT NOT NULL
);
````

### 레지스트리 감시 (Reg)

````
CREATE TABLE IF NOT EXISTS
snapshot_hklm_run(
    idx INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL
    time TEXT NOT NULL
    value TEXT NOT NULL
    type INTEGER NOT NULL
    data TEXT NOT NULL
);
````

### 네트워크 감시 (Net)

````
CREATE TABLE IF NOT EXISTS
snapshot_network(
    idx INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL
    time TEXT NOT NULL
    ip TEXT NOT NULL
    port INTEGER
);
````


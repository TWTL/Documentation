# Engine Port
- Main = 15259

# Datable Table in SQL
- Performance (Process)
```
sql = L"CREATE TABLE IF NOT EXISTS snapshot_process("
 			L"idx INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL"
 			L"time TEXT NOT NULL"
 			L"pid INTEGER"
 			L"ppid INTEGER"
 			L"process_name TEXT NOT NULL"
 			L"process_path TEXT NOT NULL);";
```
- Registry
```
sql = L"CREATE TABLE IF NOT EXISTS snapshot_hklm_run("
 			L"idx INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL"
 			L"time TEXT NOT NULL"
 			L"value TEXT NOT NULL"
 			L"type INTEGER NOT NULL"
 			L"data TEXT NOT NULL);";
```

- Network
```
sql = L"CREATE TABLE IF NOT EXISTS snapshot_network("
			L"idx INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL"
			L"time TEXT NOT NULL"
			L"ip TEXT NOT NULL"
			L"port INTEGER);";
```

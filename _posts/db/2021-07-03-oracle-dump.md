---
layout: post
title: Oracle Dump(expdp / impdp)
date: 2021-06-26 19:20:23 +0900
category: DB

---

## DB DUMP(expdp / impdp)

#### expdp

```bash
-- 전체 백업
expdp USERNAME/PASSWORD@HOST:PORT/SID directory=DUMP full=y dumpfile=FILE_NAME.dmp logfile=log.log

-- 스키마 백업
expdp USERNAME/PASSWORD@HOST:PORT/SID directory=DUMP schemas=SCHEMA_NAME dumpfile=FILE_NAME.dmp logfile=log.log

-- 테이블스페이스 백업
expdp USERNAME/PASSWORD@HOST:PORT/SID directory=DUMP tablespaces=TABLESPACE_NAME dumpfile=FILE_NAME.dmp logfile=log.log

-- 테이블 백업
expdp USERNAME/PASSWORD@HOST:PORT/SID directory=DUMP tables=SCHEMA.TABLE_NAME dumpfile=FILE_NAME.dmp logfile=log.log
```

directory 값(DUMP)는 미리 등록이 되어있어야 함

```sql
-- 1. 디렉토리 조회
SELECT * FROM SQL_DIRECTORIES;

-- 2. 디렉토리 추가
create directory dpump_dir as '/DATA/oracle';

-- 3. 디렉토리 삭제 (잘못 입력 했을 경우 삭제)
drop directory dpump_dir;

-- 4. 디렉토리에 대한 권한 설정
grant read, write on directory dpump_dir to 사용자(또는 public);
```



#### impdp

```bash
-- 전체 임포트
impdp USERNAME/PASSWORD@HOST:PORT/SID directory=DUMP dumpfile=FILE_NAME.dmp full=y

-- 테이블스페이스 임포트
impdp USERNAME/PASSWORD@HOST:PORT/SID directory=DUMP dumpfile=FILE_NAME.dmp tablespaces=TABLESPACE_NAME

-- 다른 테이블스페이스로 임포트(FROM_TABLESPACE: 백업 당시 테이블스페이스 / TO_TABLESPACE: 임포트 대상 테이블스페이스)
impdp USERNAME/PASSWORD@HOST:PORT/SID directory=DUMP dumpfile=FILE_NAME.dmp remap_tablespace=FROM_TABLESPACE:TO_TABLESPACE

-- 다른 스키마로 임포트(FROM_SCHEMA: 백업 당시 스키마 / TO_SCHEMA: 임포트 대상 스키마)
impdp USERNAME/PASSWORD@HOST:PORT/SID directory=DUMP dumpfile=FILE_NAME.dmp remap_schema=FROM_SCHEMA:TO_SCHEMA
```



##### 옵션

- **table_exists_action**
  - skip:  테이블이 존재하는 경우, 데이터 가져오기 X / content=data_only 옵션이 있을 경우 table_exists_action 옵션은 유효하지 않다.
  - truncate: 테이블이 존재하는 경우, 기존 데이터 행을 truncate하고 데이터를 가져옴. 클러스터 테이블에서는 사용 X
  - append: 테이블이 존재하는 경우, 기존 데이터 행을 변경하지 않고 가져옴
  - replace: 테이블이 존재하는 경우, 테이블 자체를 내부적으로 drop하고 다시 생성하여 데이터를 가져옴

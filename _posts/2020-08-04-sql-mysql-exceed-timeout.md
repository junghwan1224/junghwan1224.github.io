---
layout: post
title: MySQL - Lock wait timeout exceeded issue
date: 2020-08-04 19:20:23 +0900
category: SQL

---

`MySQLTransactionRollbackException: Lock wait timedout exceeded; try restarting transaction`  이슈가 발생



### 원인

- 보통 단일 트랜잭션의 수행 시간이 긴 경우

- `innodb lock wait timeout` 값이 작게 설정되어 있는 경우

  - ```mysql
    SELECT @@innodb_lock_wait_timeout;
    ```

  - 쿼리문으로 wait timeout 값 확인

  - ```mysql
    SET GLOBAL innodb_lock_wait_timeout = 10;
    ```

  - 쿼리문으로 wait timeout 값 변경, 세션에만 적용하고자 하면 `GLOBAL` 대신 `SESSION` 입력

- `Isolation level` 과 관련이 있는 경우

  - RDBMS의 Isolation level과 관련해서 SELECT 문의 실행 시간이 오래 걸리는 경우에 발생

  - READ COMMITED
    - Commit된 데이터만 읽는 것을 허용하는 레벨
    - SELECT 문이 수행되는 동안 해당 데이터에 Shared Lock이 걸리고 SELECT가 완료되면 Lock이 해제되는 것을 의미
    - 쓰기의 경우 데이터를 변경하는 동안 다른 트랜잭션은 해당 데이터를 변경할 수 없고 wait 하는 것을 의미
    - 정리하면 읽기는 SELECT, 쓰기는 트랜잭션 단위로 Lock이 걸림
    - Oracle의 Default Level
    
  - REPEATABLE READ
    - 트랜잭션 첫 SELECT에서 해당 데이터의 Shared Lock을 걸고 데이터의 Snapshot 생성
    - 이후 동일 트랜잭션 내의 SELECT는 Snapshot에서 읽게 된다.
    - MySQL의 Default Level
    
  - MySQL의 경우 어떤 트랜잭션에서 SELECT를 해서 Snapshot을 만드는 시간이 오래 걸려 timeout 설정값을 넘기게 되면 `Lock wait timedout exceeded` 이슈 발생, Snapshot 생성 시간 동안 lock을 걸기 때문

  - 해결 방법

    - Isolation level 변경

      - ```bash
        vi /etc/my.cnf
        transaction-isolation = READ-COMMITED
        ```

      - 수정하고 DB 재시작

      - Java 소스 코드 레벨에서도 변경 가능

      - ```java
        @Transactional(isolation = Isolation.READ_COMMITED)
        ```


[출처: https://brunch.co.kr/@cg4jins/8](https://brunch.co.kr/@cg4jins/8)
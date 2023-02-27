# SQL 정리

# DBMS

- 데이터베이스를 관리하는 소프트웨어
- 다양하고 대용량의 데이터를 저장할 수 있어야하며 여러 명의 사용자나 응용 프로그램과 공유 및 동시간 접근이 가능해야함 → 엑셀은 DBMS가 아님
- DBMS에는 계층형, 망형, 관계형, 객체지향형, 객체관계형 등으로 분류. 이 중 관계형 DBMS가 가장 많이 사용된다
    - 계층형 DBMS : 트리형태로 구성되어 군대식으로 보고체계를 거쳐 접근할 수 있음. 비효율적인 구조 때문에 현재는 사용 X
    - 망형 DBMS : 계층형과 유사하지만 하위 단계로의 접근은 자유롭게 가능. 하지만 프로그래머가 모든 구조를 이해해야하므로 현재는 거의 사용 X
    - 관계형 DBMS : 데이터베이스가 테이블이라는 최소 단위로 구성되며, 이 테이블은 하나 이상의 열과 행으로 구성됨
- 데이터베이스에는 테이블 외에 인덱스, 뷰, 스토어드 프로시저, 트리거, 함수, 커서 등의 개체가 필요
    - 인덱스 : 데이터를 조회할 때 결과가 나오는 속도를 획기적으로 줄여줌
    - 뷰 : 테이블의 일부를 제한적으로 표현
    - 스토어드 프로시저 : SQL에서 프로그래밍이 가능하도록 해줌
    - 트리거 : 잘못된 데이터가 들어가는 것을 미연에 방지

## SQL의 분류

- DML(Data Manipulation Language)
    - 데이터 조작 언어
    - 데이터를 조작(선택, 삽입, 수정, 삭제)하는 데 사용
    - SELECT, INSERT, UPDATE, DELETE가 해당
    - 트랜잭션(Transaction, 테이블의 데이터를 변경할 때 실제 테이블에 완전히 적용하지 않고, 임시로 적용시키는 것)도 DML에 해당
- DDL(Data Definition Language)
    - 데이터 정의 언어
    - 데이터베이스, 테이블, 뷰, 인덱스 등의 데이터베이스 개체를 생성/삭제/변경하는 역할
    - CREATE, DROP, ALTER 구문
    - ROLLBACK이나 COMMIT 사용 불가
    - DDL문은 실행 즉시 MySQL에 적용
- DCL(Date Control Language)
    - 데이터 제어 언어
    - 사용자에게 어떤 권한을 부여하거나 빼앗을 때 주로 사용
    - GRANT, REVOKE, COMMIT, ROLLBACK 구문

# MySQL

## MySQL의 데이터 형식

- 정수형
    
    
    | 데이터 형식 | 바이트 수 | 숫자 범위 |
    | --- | --- | --- |
    | TINYINT | 1 | -128 ~ 127 |
    | SMALLINT | 2 | -32768 ~ 32767 |
    | INT | 4 | 약 -21억 ~ 21억 |
    | BIGINT | 8 | 약 -900경 ~ 900경 |
- 문자형 : 개수 인자에 들어가는 수 만큼 최대로 문자를 입력할 수 있음. 필요없이 길게 지정할 경우 바이트 수는 그 개수 인자만큼 지정되기 때문에 낭비 방지하기 위해 신중히 선택 필요
    
    
    | 데이터 형식 | 바이트 수 |
    | --- | --- |
    | CHAR(개수) | 1~255 |
    | VARCHAR(개수) | 1~16383 |
- 대량의 데이터 형식
    
    
    | 데이터 형식_상위 | 데이터 형식_하위 | 바이트 수 |
    | --- | --- | --- |
    | TEXT 형식 | TEXT | 1~65535 |
    | TEXT 형식 | LONGTEXT | 1~4294967295 |
    | BLOB 형식 | BLOB | 1~65535 |
    | BLOB 형식 | LONGBLOB | 1~4294967295 |
    - 대량의 TEXT는 주로 소설이나 대본 등 긴 텍스트를 저장
    - BLOB(Binary Long Object) : 글자가 아닌 이미지, 동영상 등의 데이터. 파일 내용이 통째로 들어가 있음
- 실수형
    
    
    | 데이터 형식 | 바이트 수 | 설명 |
    | --- | --- | --- |
    | FLOAT | 4 | 소수점 아래 7자리 |
    | DOUBLE | 8 | 소수점 아래 15자리 |
- 날짜형
    
    
    | 데이터 형식 | 바이트 수 | 설명 |
    | --- | --- | --- |
    | DATE | 3 | 날짜만 저장. YYYY-MM-DD 형식 |
    | TIME | 3 | 시간만 저장. HH:MM:SS 형식 |
    | DATETIME | 8 | 날짜 및 시간을 저장. YYYY-MM-DD HH:MM:SS 형식 |
- 데이터 형식 변환
    - CAST(값 AS 데이터형식 [ ( 길이 ) ])
    - CONVERT(값, 데이터 형식 [ ( 길이 ) ])
    - `SELECT CAST(AVG(PRICE) AS SIGNED) '평균 가격' FROM BUY;` → SIGNED는 부호가 있는 정수, UNSIGNED는 부호가 없는 정수
    - `SELECT CONVERT(AVG(PRICE), SIGNED) '평균 가격' FROM BUY;`

### MYSQL 명령어 - 변수

- 변수를 선언할 수 있음
- `SET @변수명 = 변수값`
- 하지만 이 변수명을 LIMIT 문에 바로는 사용 못함. 사용하는 방법은
`SET @COUNT = 3;
PREPARE mysql from 'SELECT * FROM MEMBER LIMIT?'
EXECUTE mysql USING @COUNT;`

### 테이블 제약조건

- 기본키
    - 기본키로 지정되면 중복될 수 없고, 비어 있을 수도 없음
    - 기본키로 미리 지정해서 테이블을 생성하지 않았을 때는 
    `ALTER TABLE member
    ADD CONSTRAINT
    PRIMARY KEY (mem_id);`
- 외래키
    - 기본키 - 외래키 관계로 설정된 테이블이 있을 경우 외래키 테이블은 기본키에 존재하는 데이터만 입력이 가능하므로 기본키 테이블을 먼저 삭제할 수 없음
    - 외래키로 미리 지정해서 테이블을 생성하지 않았을 때는
    `ALTER TABLE buy
    ADD CONSTRAINT
    FOREIGN KEY(mem_id)
    REFERENCES member(mem_id);`
- 고유키(Unique)
    - 중복되지 않는 유일한 값을 입력해야 하는 조건
    - 기본키 제약조건과 거의 비슷하지만, 차이점은 고유키는 NULL값을 허용. NULL은 여러개가 입력되어도 된다.
- 체크 제약조건
    - 입력되는 데이터를 점검
    - `CREATE TABLE member
    (mem_id CHAR(8) NOT NULL PRIMARY KEY,
    mem_name VARCHAR(10) NOT NULL,
    height TINYINT UNSIGNED NULL CHECK(height >= 100));` → height가 100 이상이 입력되도록 체크
    - `ALTER TABLE member
    ADD CONSTRAINT
    CHECK (phone1 IN ('02', '031', '032', '054', '055', '061'));`
- 기본값 정의
    - 값을 입력할 때 default로 입력하면 디폴트값이 입력
    - `CREATE TABLE member
    (mem_id CHAR(8) NOT NULL PRIMARY KEY,
    mem_name VARCHAR(10) NOT NULL,
    height TINYINT UNSIGNED NULL DEFAULT 160);`
    - `ALTER TABLE member
    ALTER COLUMN phone1 SET DEFAULT '02';`

## MYSQL 명령어 - DB 확인

- MYSQL은 기본적으로 세미콜론( ; )으로 명령어와 명령어를 구분
- `SHOW DATABASES`
    - 현재 있는 모든 데이터베이스를 출력
- `USE database_name`
    - 사용할 데이터베이스를 지정
    - 다시 USE문을 사용하거나 다른 DB를 이용하겠다고 명시하지 않는 이상 모든 SQL문은 지정 DB에서 수행
    - 실행하는 순간 왼쪽 GUI에서 해당 DB가 진하게 선택됨
- `SHOW TABLES`
    - 지정한 DB의 테이블 이름 보기
- `SHOW TABLE STATUS`
    - 지정한 DB의 테이블의 설정 확인
- `DESC CITY (DESCRIBE CITY)`
    - CITY 테이블에 무슨 열이 있는지 확인

## MYSQL 명령어 - 기본구문

### SELECT 문

- 일반적으로 가장 많이 사용
- 데이터베이스 내 테이블에서 원하는 정보를 추출
- `SELECT 열
FROM 테이블
WHERE 조건식
GROUP BY { 열1 | 열2 | 열3 }
HAVING 조건식
ORDER BY { 열1 | 열2 | 열3 }`
- 만약 모든 열을 추출한다면 *로 대체
- 조건식은 관계연산자를 통해 활용(OR, AND, <, >, >=, <>, !=, NOT, IN, IS 등) 여기서 <>은 ≠이고, ==은 =만 씀
- 조건식에 `POPULATION BETWEEN A AND B`도 가능
- 조건식 LIKE로 문자열 검색 가능
    - `LIKE ‘A%’` : A뒤에 무엇이든지 허용
    - `LIKE ‘A__’` : A 뒤에 문자 두 글자가 있는 데이터만 허용
    - 서브쿼리도 가능(쿼리문 안에 또 다른 쿼리문)
        - `SELECT * FROM CITY WHERE COUNTRYCODE = (SELECT COUNTRYCODE FROM CITY WHERE NAME = ‘SEOUL’)`
        - 위의 코드는 서울이 어떤 COUNTRY에 속하는지 모를 때, 그 나라의 모든 도시들을 확인
        - ANY(= SOME) : 서브쿼리의 여러 결과 중 하나만 만족해도 가능
            - `SELECT * FROM CITY WHERE POPULATION > ANY (SELECT POPULATION FROM CITY WHERE DISTRICT = ‘NEW YORK’)`
        - ALL : 서브쿼리의 여러 결과를 모두 만족시켜야함
            - `SELECT * FROM CITY WHERE POPULATION < ALL (SELECT POPULATION FROM CITY WHERE DISTRICT = ‘NEW YORK’)`
- `ORDER BY` : 기본적으로 오름차순 정렬, 내림차순으로 할 경우 열 이름 뒤에 `DESC` 적어주기
    - 여러개 적을 경우 앞의 열 기준 먼저
- `LIMIT` : 몇 건을 출력할지 지정
    - `SELECT * FROM MEMBER LIMIT 3;`
- `DISTINCT` : 중복된 것은 1개씩만 보여주면서 출력
    - `SELECT DISTINCT COUNTRYCODE FROM CITY`
- `LIMIT` : 출력 개수를 제한. 상위 N개만 출력하는 구문
    - `SELECT * FROM CITY LIMIT 10`
- `GROUP BY` : 집계함수를 함께 사용
    - `SELECT COUNTRYCODE, MAX(POPULATION) FROM CITY GROUP BY COUNTRYCODE`
    - AVG( ), MIN( ), MAX( ), COUNT( ), COUNT(DISTINCT ), STDEV( ), VARIANCE( )
- `HAVING` : WHERE과 비슷한 개념. 반드시 `GROUP BY` 다음에 나와야함
    - `SELECT COUNTRYCODE, MAX(POPULATION) FROM CITY GROUP BY COUNTRYCODE HAVING MAX(POPULATION) > 8000000`
- `ROLLUP` : 총합 혹은 중간합계가 필요할 경우 사용, `GROUP BY` 절과 함께 `WITH ROLLUP` 문 사용
    - `SELECT COUNTRYCODE, NAME, SUM(POPULATION) FROM CITY GROUP BY COUNTRYCODE, NAME WITH ROLLUP`
- `AS` : 읽기 좋게 하기 위해 별칭 사용
    - `SELECT COUNTRYCODE, MAX(POPULATION) AS 'POPULATION' FROM CITY GROUP BY COUNTRYCODE`

### 조인

- `JOIN` : 데이터베이스 내의 여러 테이블에서 가져온 레코드를 조합
    - `SELECT * FROM CITY JOIN COUNTRY ON CITY.COUNTRYCODE = COUNTRY.CODE` : CITY의 COUNTRYCODE와 COUNTRY의 CODE가 같은 것을 조인
    - 여러개의 테이블을 JOIN할 경우 하나씩 JOIN문을 여러개 작성
- 내부조인 : JOIN과 동일한 의미. 일부 열만 추출할 경우 양쪽 테이블에서 동일한 컬럼명인 mem_id를 그대로 쓰면 안되고 buy.mem_id 또는 member.mem_id로 써야함
    
    `SELECT *
    FROM buy
    INNER JOIN member
    ON buy.mem_id = member.mem_id`
    
    - mem_id 같이 겹치는 컬럼명이 많아서 코드가 너무 복잡할 때는 FROM buy AS B 이런식으로 테이블 명을 간결하게 만들어줘서 B.mem_id 등의 형태로 입력 가능
- 외부조인 : 두 테이블을 조인할 때 왼쪽/오른쪽에 있는 값에 조인 시행(없을 시 NULL). 내부조인은 둘 중에 하나라도 NULL 값이 있으면 아예 나오지 않았음
    
    `SELECT M.mem_id, M.mem_name, B.prod_name, M.addr
    FROM buy B
    RIGHT OUTER JOIN MEMBER M
    ON M.mem_id = B.mem_id;`
    
    - OUTER을 생략해서 RIGHT JOIN / LEFT JOIN으로만 쳐도 입력 가능
- 상호조인 : 한쪽 테이블의 모든 행과 다른쪽 행의 모든 행을 조인. 외래키를 지정할 수 없어 모든 경우의 수에 맞게 조인됨. A테이블이 10행, B테이블이 12행이라면 총 120행이 생성된다.
`SELECT *
FROM buy
CROSS JOIN member;`
- 자체조인 : 자기 자신과 조인. 자주 사용되진 않는다.

## MYSQL 내장함수

- 문자열 함수, 수학 함수, 날짜와 시간 함수 등
- `LENGTH('text')` : 문자열 길이
- `CONCAT('text1', 'text2', ...)` : 전달받은 문자열을 모두 결합하여 하나의 문자열
    - `SELECT CONCAT('MY', 'SQL OP', 'EN SOURCE)`
- `LOCATE('findtext', 'source')` : 문자열 내에서 찾는 문자열이 처음으로 나타나는 위치를 찾아서 해당 위치를 반환. MYSQL에선 시작인덱스가 1임
    - `SELECT LOCATE('ABC', 'ABDASDASAGABC')`
- `LEFT('text', num)`, `RIGHT('text', num)` : 문자열의 왼/오른쪽으로부터 지정한 개수만큼의 문자를 반환
    - SELECT LEFT(’MYSQL is’, 4)
- `LOWER('text')`, `UPPER('text')` : 소문자, 대문자 변환
- `REPLACE('source', 'target', 'to')` : 문자열 해당부분 변경
    - `SELECT REPLACE('MSSQL', 'MS', 'MY)` : MYSQL에서 MS를 MY로 변경
- `TRIM(Direction 'remove_text' FROM source)` : 방향에 따라 지정한 텍스트 삭제. 지정안할시 양쪽 공백 제거
    - Direction : Both(양쪽), Leading(왼쪽), Trailing(오른쪽)
- `FORMAT(number, shape)` : 세자리마다 쉼표가 생기고, 소수자리수만큼 반환
    - `SELECT FORMAT(123123123123.123123123123, 3)` → 123,123,123,123.123
- `FLOOR(number)`, `CEIL(number)`, `ROUND(number)` : 내림, 올림, 반올림
- `SQRT(n)`, `POW(a, b)` =  $a^b$, `EXP(n)` = $e^n$, `LOG(n)`
- `SIN(n)`, `COS(n)`, `TAN(n)`
- `ABS(n)`, `RAND( )` : 절댓값, 0~1 사이 실수 랜덤
- `NOW( )`, `CURDATE( )`, `CURTIME( )` : 각각 현재 날짜와 시간, 날짜, 시간을 반환
- `DATE(time)`, `DAY(time)`, `HOUR(time)`, `MINUTE(time)`, `SECOND(time)` : 전달받은 값에 해당하는 날짜, 일, 시간, 분, 초를 반환
- `MONTHNAME(time)`, `DAYNAME(time)`
- `DAYOFWEEK(time)`, `DAYOFMONTH(time)`, `DAYOFYEAR(time)` : 각각 일자가 해당 주에서 몇번째 날(일요일 = 1), 해당 월에서 몇번째 날, 해당 연도에서 몇번째 날
- `DATE_FORMAT(time, ‘FORMAT’)` : 특정 날짜를 FORMAT 형식대로 사용. ‘%D %y %a %d %m %n %j’ 등으로 씀

## MYSQL 고급함수

- `CREATE TABLE 테이블명 AS SELECT * FROM CITY` : CITY 테이블 그대로 테이블명대로 새로운 테이블을 만듬 → GUI에서도 가능
    - `CREATE TABLE test2 (
    id INT NOT NULL PRIMARY KEY,
    col1 INT NULL,
    col2 FLOAT NULL,
    col3 VARCHAR(45) NULL
    )`
    - 열을 생성할 때 `AUTO_INCREMENT` 를 열 정보에 입력하면 자동으로 1씩 올라감
    `ID INT AUTO_INCREMENT PRIMARY KEY`
    - 외래키를 설정할 경우 마지막에 `FOREIGN KEY () REFERENCES ()`로 지정
    `CREATE TABLE buy
    (num INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
    mem_id CHAR(8) NOT NULL,
    prod_name CHAR(6) NOT NULL,
    group_name CHAR(4) NULL,
    price INT UNSIGNED NOT NULL,
    amount SMALLINT UNSIGNED NOT NULL,
    FOREIGN KEY(mem_id) REFERENCES member(mem_id)` → member 테이블의 mem_id에 연결
- `CREATE DATABASE DB명` : DB명대로 새로운 DB 생성, USE문으로 열고 사용
- `ALTER TABLE` : 테이블 변경
    - `ALTER TABLE test2 
    ADD col4 INT NULL` → 컬럼 추가
    - `ALTER TABLE test2
    MODIFY col4 VARCHAR(20)` → 데이터 타입 변경
    - `ALTER TABLE test2
    DROP col4` → 컬럼 삭제
    - `ALTER TABLE test2 AUTO_INCREMENT=100;` → AUTO_INCREMENT의 시작값을 100으로 설정
    - `SET @@AUTO_INCREMENT_INCREMENT = 3;` → AUTO_INCREMENT의 증가값을 3으로 설정
- 인덱스 : 테이블에서 원하는 데이터를 빠르게 찾기 위해 사용. 인덱스가 있는 테이블은 처리속도가 느려질 수 있음. 그러므로 수정보단 검색이 자주 사용되는 테이블에서 사용
    - 기본키로 설정해서 자동으로 생성된 **클러스터형 인덱스**와 고유키 및 직접 따로 지정하는 **보조 인덱스**가 존재
    - 클러스터형 인덱스는 데이터를 자동으로 정렬해주지만, 보조인덱스는 그렇지 않음
    - 보조인덱스를 적용하려면 `ANALYZE TABLE` 문으로 테이블을 처리해주어야함
    - `CREAT INDEX Col1Idx
    ON test (col1)`  → test 테이블의 col1열을 (보조) 인덱스 설정. 인덱스 이름은 Col1Idx
    - `ANALYZE TABLE test;` → 보조인덱스를 적용
    - `SHOW INDEX FROM test` → 인덱스 확인. 기본키는 항상 인덱스를 가짐
    - `CREATE UNIQUE INDEX COL2IDX
    ON TEST (COL1)` → 중복값을 못가지는 인덱스 형성
    - `ALTER TABLE TEST
    ADD FULLTEXT Col3Idx (col3)`  → 일반적인 인덱스와 달리 매우 빠르게 테이블의 모든 텍스트 컬럼 검색이 가능하게끔 인덱스 형성
    - `ALTER TABLE test
    DROP INDEX Col3Idx`  → 인덱스 삭제 방법 1
    - `DROP INDEX Col2Idx ON test`   → 인덱스 삭제 방법 2
- View : DB에 존재하는 가상 테이블. 데이터를 보여주는 역할만 수행. 여러 테이블이나 뷰를 하나의 테이블처럼 볼 수 있다.
    - 필요한 컬럼만, 복잡한 쿼리를 단순화해서 사용 가능
    - 원본 데이터를 건드리지 못하므로 보안적으로 좋음
    - 하지만 한번 정의된 뷰는 바꿀 수 없으며, 삽입 삭제 갱신 등 수정작업에 많은 제약
    - `CREATE VIEW testView AS
    SELECT Col1, Col2
    FROM test`  → test 테이블의 Col1, Col2를 가져온 뷰를 생성
    - `ALTER VIEW testView AS
    SELECT Col1, Col2, Col3
    FROM test`  → 수정
    - `DROP VIEW testView`  → View 삭제
    - `SELECT city[.](http://city.name/)name, country.surfacearea, city.population, countrylanguage.language
    FROM city
    JOIN country ON city.countrycode = country.code
    JOIN countrylanguage on city.countrycode = countrylanguage.countrycode
    WHERE city.countrycode = 'KOR';` → 마치 테이블인것 처럼 SELECT 문 가능
- `INSERT` : 행 삽입
    - `INSERT INTO test
    VALUE(1, 123, 1.1, ‘TEST’)`
    - GUI에서도 `SELECT * FROM TEST`에서 나온 상태에서 테이블에 직접 입력하고, 오른쪽 아래 APPLY를 눌러서 적용 가능
    - `INSERT INTO TEST2 SELECT * FROM TEST`  → TEST내용을 TEST2에 삽입
    - 결측값이 있을 경우엔 `INSERT INTO test VALUE(1, NULL, NULL, NULL)`
    - 또는 `INSERT INTO (id, col1) VALUE (1, 2)`
- `UPDATE` : 값 수정
    - `UPDATE TEST
    SET col1 = 1, col2 = 1.0, col3=’TE’
    WHERE ID = 1`   → WHERE문 안쓰면 다른 모든 데이터가 변경되기 때문에 꼭 쓰기
    - `UPDATE TEST
    SET POPULATION = POPULATION / 10000;` → 단위를 바꾸는 것 처럼 전체 값을 바꾸고 싶을 땐 이렇게 WHERE문 없이 가능
- `DELETE` : 행 단위로 데이터 삭제
    - `DELETE FROM test
    WHERE ID = 1`   → WHERE문 꼭 써야 해당 데이터만 줄음
    - DELETE 문으로 데이터를 지우더라도 롤백(복구)가 가능하기 때문에, 테이블 용량은 줄지 않는다.
- `TRUNCATE` : 용량도 줄어들고 데이터 복구 불가능. 모든 데이터를 지움. 테이블의 구조는 남길 수 있음. DROP TABLE은 테이블을 지우고, TRUNCATE TABLE은 테이블의 데이터를 지움
    - `TRUNCATE TABLE test`
- `DROP` : 테이블/데이터베이스 전체를 삭제. 복구 불가. 구조 안남음
    - `DROP TABLE test`  → 테이블 삭제
    - `DROP DATABASE SUAN`  → DB 삭제
    

## SQL 프로그래밍

- 기본적으로 Stored Procedure 안에 만들어야 한다.
- IF 문
    - `IF <조건식> THEN
        <실행문>
    END IF;`
    - `DELIMITTER $$
    CREATE PROCEDURE ifProc1( )
    BEGIN
    IF 100 = 100 THEN
    SELECT ‘100은 100과 같습니다.’;
    END IF;
    END $$
    DELIMITER;
    CALL ifProc1( );`
- IF ~ ELSE 문
    - `DELIMITER %%
    CREATE PROCEDURE ifproc2( )
    BEGIN
    DECLARE mynum INT;       — mynum을 int형으로 선언
    SET mynum = 200;
    IF mynum = 200 THEN
    SELECT ‘100입니다.’;
    ELSE
    SELECT ‘100이 아닙니다.’;
    END IF;
    END %%
    DELIMITER ;`
- CASE 문
    - IF문이 여러개가 필요할 때 CASE문을 사용
    - `CREATE PROCEDURE caseproc( )
    BEGIN
    DECLARE point INT;
    DECLARE credit CHAR(1);
    SET point = 88;
    
    CASE
    WHEN point >= 90 THEN
    SET credit = 'A';
    WHEN point >= 80 THEN
    SET credit = 'B';
    WHEN point >= 70 THEN
    SET credit = 'C';
    WHEN point >= 60 THEN
    SET credit = 'D';
    ELSE
    SET CREDIT = 'F';
    END CASE;
    SELECT CONCAT('취득점수==>', point), CONCAT('학점==>', credit);`
    - `SELECT B.mem_id, m.mem_name, b.prod_name, SUM(price**amount) as '총구매액',*
    **CASE**
    **WHEN (SUM(price*amount) >= 1500) THEN '최우수고객'
    WHEN (SUM(price*amount) >= 1000) THEN '우수고객'
    WHEN (SUM(price*amount) >= 1) THEN '일반고객'
    ELSE '유령고객'
    END '회원금액'
    FROM buy AS B
    RIGHT JOIN member AS M
    ON B.mem_id = M.mem_id
    GROUP BY M.mem_id
    ORDER BY SUM(price*amount) DESC;`
- WHILE 문
    - `WHILE <조건식> DO
     <실행식>
    END WHILE;`
    - `DELIMITER %%
    CREATE PROCEDURE whileproc()
    BEGIN
    DECLARE i INT;
    DECLARE hap INT;
    SET i = 1;
    SET hap = 0;
    WHILE(i <= 100) DO
    SET hap = hap + i;
    SET i = i + 1;
    END WHILE;
    END %%
    DELIMITER ;`
    - `DELIMITER $$
    CREATE PROCEDURE whileProc2()
    BEGIN
    DECLARE i INT; -- 1에서 100까지 증가할 변수
    DECLARE hap INT; -- 더한 값을 누적할 변수
    SET i = 1;
    SET hap = 0;
    myWhile:
    WHILE (i <= 100) DO  -- While문에 label을 지정
       IF (i%4 = 0) THEN
         SET i = i + 1;
         ITERATE myWhile; -- 지정한 label문으로 가서 계속 진행
       END IF;
       SET hap = hap + i;
       IF (hap > 1000) THEN
         LEAVE myWhile; -- 지정한 label문을 떠남. 즉, While 종료.
       END IF;
       SET i = i + 1;
    END WHILE;
    SELECT '1부터 100까지의 합(4의 배수 제외), 1000 넘으면 종료 ==>', hap;
    END $$
    DELIMITER ;`
- 동적 SQL : 변경되는 내용을 실시간으로 저장
    - `CREATE TABLE gate_table (id INT AUTO_INCREMENT PRIMARY KEY, entry_time DATETIME);
    SET @curDate = CURRENT_TIMESTAMP(); -- 현재 날짜와 시간
    PREPARE myQuery FROM 'INSERT INTO gate_table VALUES(NULL, ?)';
    EXECUTE myQuery USING @curDate;
    DEALLOCATE PREPARE myQuery;`
    

### 스토어드 프로시저(Stored Procedure)

- 파이썬의 def 문처럼 자주 사용하는 기능에 대해 사용자 정의함수를 만드는 것
- `SELECT * FROM member WHERE member_name = ‘나훈아’;
 SELECT * FROM product WHERE product_name = ‘삼각김밥’;`
- 만약 위 두 줄을 자주 사용한다면,
- `DELIMITER //
CREATE PROCEDURE myProc( )
BEGIN
        SELECT * FROM member WHERE member_name = ‘나훈아’;
        SELECT * FROM product WHERE product_name = ‘삼각김밥’;
END //
DELIMITER ;`
`CALL myProc( );`
- 이렇게 call function( ) 으로 사용자정의함수처럼 사용이 가능하다.
- `DELIMITER $$
CREATE PROCEDURE user_proc1(IN username VARCHAR(10))
BEGIN
SELECT * FROM member WHERE mem_name = username;
END $$
DELIMITER ;`
-> IN 매개변수이름 매개변수형식의 형태로 입력매개변수 지정 가능
- `DELIMITER $$
CREATE PROCEDURE user_proc3(
IN txtvalue CHAR(10),
OUT outvalue INT)
BEGIN
INSERT INTO notable VALUES(NULL, txtvalue)
SELECT MAX(id) INTO outvalue FROM notable;  -- SELECT __ INTO __ FROM _ : 그 값을 입력함
END $$
DELIMITER ;`
—> OUT 매개변수이름 매개변수형식, 함수 코드의 SELECT문으로 출력 매개변수에 데이터 저장
- `DELIMITER $$
CREATE PROCEDURE ifelse_proc(
IN memname VARCHAR(10))
BEGIN
DECLARE debutyear int
SELECT YEAR(debut_date) INTO debutyear FROM member WHERE mem_name = memname
IF (debutyear >= 2015) THEN
SELECT '신인 가수네요. 화이팅 하세요.' AS '메시지';
ELSE
SELECT '고참 가수네요. 그동안 수고하셨어요.' AS '메시지';
END IF;
END $$
DELIMITER ;`
-> DECLARE문으로 지역변수처럼 선언해서 SELECT INTO문으로 값을 넣어주고, 그 지역변수를 활용한 프로그래밍도 가능

- 커서 : 테이블에서 한 행씩 처리하기 위한 방식. 주로 스토어드 프로시저와 함께 사용
    - 회원의 평균 인원수를 구하는데, 커서를 활용해 한 행씩 회원의 인원수를 누적시키는 방법으로 처리하는 예시
    - `DELIMITER $$
    CREATE PROCEDURE cursor_proc()
    BEGIN
    DECLARE memnumber INT;
    DECLARE cnt INT DEFAULT 0;
    DECLARE totnumber INT DEFAULT 0;
    DECLARE endofrow BOOLEAN DEFAULT FALSE;
    DECLARE membercursor CURSOR FOR    -- 커서 이름 membercursor로 커서를 선언
    SELECT mem_number FROM member;
    
    DECLARE CONTINUE HANDLER    -- 반복 조건을 준비하는 에약어
    FOR NOT FOUND SET endofrow = TRUE;    -- FOR NOT FOUND SET(더이성 행이 없을 때) TRUE대입
    
    OPEN membercursor:
    
    cursor_loop: LOOP
    FETCH membercursor INTO memnumber;  -- 한행씩 읽기. memnumber 변수에 각 mem_number을 대입
    IF endofrow THEN
    LEAVE CURSOR_loop;
    END IF;
    SET cnt = cnt + 1;
    SET totnumber = totnumber + memnumber;
    END LOOP cursor_loop;
    
    SELECT (totnumber/cnt) AS '회원의 평균 인원 수';
    CLOSE membercursor;
    END $$
    DELIMITER;`

### 스토어드 함수(Stored Function)

- 스토어드 프로시저와 비슷하지만 얘는 CALL이 필요 없음. SELECT문에 함수처럼 적용 가능
- `DELIMITER $$
CREATE FUNCTION sumfunc(number1 INT, number2 INT)
RETURNS INT    -- 반환데이터 형식 정수로 지정
BEGIN
RETURN number1 + number2
END $$
DELIMITER;
SELECT sumfunc(100, 200) AS '합계'`
- `DELIMITER $$
CREATE FUNCTION calcyearfunc(dyear INT)
RETURNS INT
BEGIN
DECLARE runyear INT;
SET runyear = YEAR(CURDATE()) - dyear;
RETURN runyear;
END $$
DELIMITER;`
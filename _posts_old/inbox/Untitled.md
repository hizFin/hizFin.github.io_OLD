# Untitled

**오라클 물리적 구성 요소** 

오라클 데이터베이스의 물리적 구성 요소 : 프로셋, 메모리 , 파일 ...
오라클 파일
- 데이터 파일 : 실제 데이터가 저장
- 리두 로그 파일 : 장애가 발생했을 때 복구용으로 활용
- 컨트롤 파일 : 데이터 파일과 리두 로그 파일의 위치를 가지고 있음
- 파라미터 파일 : 데이터베이스 파라미터를 저장
...


논리적 구성 요소와 데이터 파일의 관계
![[../git/hizFin.github.io/_posts_old/inbox/attachments/1671104474.png]]



테이블 정보 조회 관련 테이블
TAB : 테이블 정보
USER_TAB_COLS : 컬럼 정보
USER_CATALOG : 현재 사용자가 소유한 테이블, 인덱스, 뷰 ...
ALL_CATALOG : 현재 사용자가 접근 가능한 테이블, 인덱스, 뷰 ...

컬럼 순서 변경
12c 이전 방법
오라클12C 버전 이전이라고 가정을 하고 tel 컬럼을 삭제 후  컬럼 순서상 맨 앞으로 tel 컬럼을 NULL을 허용하면서 기본값을 ‘000-0000-0000’으로 설정하여 추가해 봅시다. tel, empno, ename 순서 입니다

ALTER TABLE EMP10 DROP COLUMN TEL;
❶ALTER TABLE EMP10 ADD TEL VARCHAR2(20) DEFAULT '000-0000-0000';
❷CREATE TABLE EMP_TEMP AS SELECT TEL, EMPNO, ENAME FROM EMP10;
➌DROP TABLE EMP10;
➍RENAME EMP_TEMP TO EMP10;
SELECT * FROM EMP10;

12c 이후 방법 : (invisible -> visible 가장 마지막 순서로 자동 변경)
ALTER TABLE EMP10 DROP COLUMN TEL;
❶ALTER TABLE EMP10 ADD (TEL VARCHAR2(20) );
❷ALTER TABLE EMP10 MODIFY (EMPNO INVISIBLE, ENAME INVISIBLE);
➌ALTER TABLE EMP10 MODIFY (EMPNO VISIBLE, ENAME VISIBLE);
SELECT * FROM EMP10;
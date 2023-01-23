---
# NO : 003
title: "[ORA][함수] GROUPING SETS 와 ROLLUP 비교 (+ GROUPING_ID)"
author: YJ Choe
date: 2023-01-04 18:37:00 +0900
categories: [ORACLE, 함수]
tags: [study, oracle, 함수, GROUPING SETS, ROLLUP, GROUPING_ID]
img_path : /assets/img/post/
math: true
mermaid: true
---

![img1](003_01.png)

## GROUPING SETS 와 ROLLUP 비교  (+ GROUPING_ID)

차이점 : 소계합계를 구할 때 GROUPING SETS가 더 유연하게 내가 원하는 그룹만 추출할 수 있다.

### 1. GROUPING SETS

#### 특징
1. 특정 그룹별로 소계/합계 가능
- 예시) GROUP BY GROUPING SETS (A, B, C)  
  - (A) 소계/합계 + (B) 소계/합계 + (C) 소계/합계  
  
2. 전체 소계/합계 추출 시 ``()`` 입력해야 함
- 예시) GROUP BY GROUPING SETS (JOB, ENAME, ``()``)  
#### SQL
```sql
SELECT 
      CASE WHEN GROUPING_ID(A.ENAME) = '1' AND GROUPING_ID(A.JOB) = '1' 
                 -- GROUPING_ID : 해당 값이 NULL 이면 '1' , NOT NUL 이면 '0' 반환
      	   THEN '합계'
      	   ELSE A.ENAME
      END AS ENAME
,      A.JOB
,      SUM(A.SAL)
,      COUNT(1)
FROM S_MGA.EMP A 
GROUP BY GROUPING SETS(JOB, (ENAME,JOB), ()) -- (JOB), (ENAME, JOB), (전체)의 소계/합계
ORDER BY CASE WHEN ENAME = '합계' THEN 3
              WHEN ENAME IS NULL THEN 2
              WHEN ENAME IS NOT NULL THEN 1
         END, ENAME, A.JOB
```
![sql1 result](003_02.png)

### 2. ROLLUP

#### 특징 
1. ROLLUP() 함수 내 **컬럼 기입 순서**에 따라 그루핑이 달라짐
- 명시한 컬럼과 순서가 결과에 영향을 끼치며 그룹을 묶을 때 가장 오른쪽 부터 하나씩 소거해간다.  
- 예시) GROUP BY ROLLUP (A, B, C)  
  - (A,B,C) 소계/합계 + (A,B) 소계/합계 + (A) 소계/합계 + (전체) 소계/합계  

2. 전체 소계/합계 자동 추출
- 따로 명시하지 않아도 자동으로 추출 됨

#### SQL
```sql
SELECT 
       CASE WHEN GROUPING_ID(A.ENAME) = '1' AND GROUPING_ID(A.JOB) = '1'
                 -- GROUPING_ID : 해당 값이 NULL 이면 '1' , NOT NUL 이면 '0' 반환       
       	   THEN '합계'
       	   ELSE A.ENAME
       END AS ENAME
,      A.JOB
,      SUM(A.SAL)
,      COUNT(1)
FROM S_MGA.EMP A 
GROUP BY ROLLUP(JOB, ENAME) -- (JOB,ENAME), (JOB), (전체)의 소계 합계 
ORDER BY A.ENAME, A.JOB
```
![sql2 result](003_02.png)
    

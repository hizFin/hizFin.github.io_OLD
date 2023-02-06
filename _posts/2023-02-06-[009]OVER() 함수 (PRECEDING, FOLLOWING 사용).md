---
# NO : 009
title: "[Sql] OVER() 함수 (PRECEDING, FOLLOWING 사용))"
author: YJ Choe
date: 2023-02-06 22:00:00 +0900
categories: [Sql, 함수]
tags: [sql, oracle, postgresql, 함수, preceding, following, over]
img_path : /assets/img/post/
#math: true
#mermaid: true
#pin : true
---
![img1](009_01.png){: width="333"}


OVER() 함수의 기본적인 사용법을 알아보자

## OVER() 함수 _ 기본

```sql
SELECT A.ENAME
,      A.JOB
,      A.HIREDATE
,      A.SAL
,      FIRST_VALUE(SAL) OVER(PARTITION BY A.JOB ORDER BY A.SAL) -- 첫번째 값
,      LAST_VALUE(SAL)  OVER(PARTITION BY A.JOB ORDER BY A.SAL) -- 마지막 값
,      LEAD(SAL)        OVER(PARTITION BY A.JOB ORDER BY A.SAL) -- 다음 row 값
,      LAG(SAL)         OVER(PARTITION BY A.JOB ORDER BY A.SAL) -- 이전 row 값
,      RANK()           OVER(PARTITION BY A.JOB ORDER BY A.SAL) -- RANK
,      DENSE_RANK()     OVER(PARTITION BY A.JOB ORDER BY A.SAL) -- RANK_중복 순위 이후에 순차적 증가  
,      ROW_NUMBER()     OVER(PARTITION BY A.JOB ORDER BY A.SAL) -- ROW_NUMBER
,      CUME_DIST()      OVER(PARTITION BY A.JOB ORDER BY A.SAL) -- 직업별 누적분포(급여순)
,      PERCENT_RANK()   OVER(PARTITION BY A.JOB ORDER BY A.SAL) -- 직업별 백분위(급여순)
FROM S_MGA.EMP A
ORDER BY A.JOB, A.SAL
```
{: .nolineno }

![img1](009_02.png)


## PRECEDING, FOLLOWING 사용

PRECEDING, FOLLOWING을 사용하여 range(row 또는 기간)를 설정할 수 있다.

```sql

/*
 
회사의 성적이 좋아 새로운 조건들로 급여인상을 진행한다.

조건
CASE1(NEW_SAL_BY_ROW)   : 본인보다 먼저 고용된 인원 2명의 급여를 비교하여 가장 최댓값으로 산정(직업별) 
CASE2(NEW_SAL_BY_MONTH) : 본인보다 24개월 고용된 인원의 급여를 비교하여 가장 최댓값으로 산정(직업별)

*/

SELECT A.ENAME
,      A.JOB
,      A.HIREDATE
,      A.SAL  -- 기존월급
, MAX(A.SAL) OVER(PARTITION BY A.JOB         -- 직업별
                ORDER       BY A.HIREDATE
                ROWS BETWEEN 2 PRECEDING     -- 이전 2ROW 
                     AND     0 FOLLOWING     -- 이후 0ROW
                ) AS NEW_SAL_BY_ROW          -- BY 본인보다 먼저 고용된 인원 인원급여 이용한 새로운 월급 
, MAX(A.SAL) OVER(PARTITION BY A.JOB         -- 직업별
                ORDER       BY A.HIREDATE 
                RANGE BETWEEN INTERVAL '24' MONTH PRECEDING  -- 24개월 이전 
                      AND     INTERVAL '0'  MONTH FOLLOWING  -- 0개월 이후
                ) AS NEW_SAL_BY_MONTH        -- BY 본인보다 24개월 내 먼저 고용된 인원급여 이용한 새로운 월급  
FROM S_MGA.EMP A
ORDER BY A.JOB, A.HIREDATE
```
{: .nolineno }

![img1](009_03.png)


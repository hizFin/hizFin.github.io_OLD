---
# NO : 008
title: "[Postgres] 나누기 결과값의 소수점이 절삭되는 경우"
author: YJ Choe
date: 2023-01-24 15:07:00 +0900
categories: [Sql, Postgres]
tags: [postgresql, tip]
img_path : /assets/img/post/
#math: true
#mermaid: true
#pin : true
---
![img1](008_01.png){: width="333"}


postgresql로 나누기를 할 때 결과값의 소수점이 절삭되는 경우가 있다.  
어떻게 하면 온전하게 값이 출력되는지 알아보자 

## Postgres

아래에서는 상수를 기입했지만 일반적으로는 컬럼으로 대입하게 되는데 이런 경우를 봤을 때 `::decimal`이 더욱 유용하게 쓰이겠다.
```sql
SELECT 2/3                    AS COL1 -- 0
,      CAST(2/3 AS NUMERIC)   AS COL2 -- 0
,      CAST(2 AS NUMERIC) / 3 AS COL5 -- 0.66666666666666666667
,      2./3                   AS COL3 -- 0.66666666666666666667
,      2/3.                   AS COL4 -- 0.66666666666666666667
,      2/3 ::DECIMAL          AS COL6 -- 0.66666666666666666667
;
```
{: .nolineno }


## 번외 (Oracle은?)

기본적으로 절삭되지 않는다.
```sql
SELECT 2/3 AS COL1   -- 0.6666666666666666666666666666666666666667
FROM DUAL;
```
{: .nolineno }

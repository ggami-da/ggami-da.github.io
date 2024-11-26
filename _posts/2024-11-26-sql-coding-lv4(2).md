---
layout: post
title:  "[SQL] Programmers - 연간 평가점수에 해당하는 평가 등급 LV4"
date:   2024-11-24
last_modified_at: 2024-11-24
categories: [SQL]
tags: [SQL, Data]
---

{: .box-success}
프로그래머스 SQL 문제를 기반으로 설명합니다.

# 문제 내용

사실 상 문제 내용이 명확하지 않아서 약간은 별로였던 문제입니다.  
- **목표**: HR_DEPARTMENT, HR_EMPLOYEES, HR_GRADE 테이블을 이용하여 사원별 성과금 정보를 조회
- **조회할 컬럼**: 
  - 사번 (EMP_NO)
  - 성명 (EMP_NAME)
  - 평가 등급 (GRADE)
  - 성과금 (BONUS)
  
- **평가 등급 및 성과금 기준**:
  - **평가 점수** 96 이상 -> **등급**: S, **성과금**: 20%
  - **평가 점수** 90 이상 -> **등급**: A, **성과금**: 15%
  - **평가 점수** 80 이상 -> **등급**: B, **성과금**: 10%
  - 그 외 -> **등급**: C, **성과금**: 0%

- **정렬 기준**: 결과는 사번(EMP_NO)을 기준으로 오름차순 정렬


## 예제 데이터

HR_DEPARTMENT 테이블 : 회사의 부서 정보를 담고 있는 테이블입니다.

| Column Name    | Type     | Nullable | Description   |
|----------------|----------|----------|---------------|
| DEPT_ID        | VARCHAR  | FALSE    | 부서 ID (각 부서의 고유한 식별자) |
| DEPT_NAME_KR   | VARCHAR  | FALSE    | 국문 부서명 (부서의 한국어 이름) |
| DEPT_NAME_EN   | VARCHAR  | FALSE    | 영문 부서명 (부서의 영어 이름) |
| LOCATION       | VARCHAR  | FALSE    | 부서 위치 (부서가 위치한 장소) |

---

HR_EMPLOYEES 테이블 : 회사의 사원 정보를 담고 있는 테이블입니다.

| Column Name    | Type     | Nullable | Description   |
|----------------|----------|----------|---------------|
| EMP_NO         | VARCHAR  | FALSE    | 사번 (각 사원의 고유한 식별자) |
| EMP_NAME       | VARCHAR  | FALSE    | 성명 (사원의 이름) |
| DEPT_ID        | VARCHAR  | FALSE    | 부서 ID (사원이 속한 부서의 ID) |
| POSITION       | VARCHAR  | FALSE    | 직책 (사원의 직책) |
| EMAIL          | VARCHAR  | FALSE    | 이메일 (사원의 이메일 주소) |
| COMP_TEL       | VARCHAR  | FALSE    | 전화번호 (사원의 회사 전화번호) |
| HIRE_DATE      | DATE     | FALSE    | 입사일 (사원이 입사한 날짜) |
| SAL            | NUMBER   | FALSE    | 연봉 (사원의 연봉) |

---

HR_GRADE 테이블 : 2022년 사원의 평가 정보를 담고 있는 테이블입니다.

| Column Name    | Type     | Nullable | Description   |
|----------------|----------|----------|---------------|
| EMP_NO         | VARCHAR  | FALSE    | 사번 (사원의 고유한 식별자) |
| YEAR           | NUMBER   | FALSE    | 연도 (평가 연도) |
| HALF_YEAR      | NUMBER   | FALSE    | 반기 (1: 상반기, 2: 하반기) |
| SCORE          | NUMBER   | FALSE    | 평가 점수 (사원의 평가 점수) |

## SQL 쿼리

사실 상 테이블 관계를 보면 3개의 테이블을 모두 사용할 필요가 없고 HR_EMPLOYEES, HR_GRADE 테이블만 활용하면 됩니다.  
또한, 연간이기 때문에 스코어 값을 평균으로 구한 기준으로 평가 점수 및 성과급을 측정하면 됩니다.  


```sql
SELECT t1.EMP_NO
     , t1.EMP_NAME
     , CASE WHEN AVG(t2.SCORE) >= 96 THEN 'S'
            WHEN AVG(t2.SCORE) >= 90 THEN 'A'
            WHEN AVG(t2.SCORE) >= 80 THEN 'B' ELSE 'C' END AS GRADE
     , CASE WHEN AVG(t2.SCORE) >= 96 THEN MAX(t1.SAL) * 0.2
            WHEN AVG(t2.SCORE) >= 90 THEN MAX(t1.SAL) * 0.15
            WHEN AVG(t2.SCORE) >= 80 THEN MAX(t1.SAL) * 0.1 ELSE 0 END AS BONUS
FROM HR_EMPLOYEES AS t1
INNER JOIN HR_GRADE AS t2
ON t1.EMP_NO = t2.EMP_NO
GROUP BY 1, 2
```
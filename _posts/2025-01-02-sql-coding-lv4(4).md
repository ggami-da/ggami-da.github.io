---
layout: post
title:  "[SQL] Programmers - Frontㄷ개발자 찾기 LV4"
date:   2025-01-02
last_modified_at: 2025-01-02
categories: [SQL]
tags: [SQL, Data]
---

{: .box-success}
프로그래머스 SQL 문제를 기반으로 설명합니다.

# 문제 내용

## 문제 설명

주어진 문제는 FRONTEND 개발자의 정보를 조회하는 것입니다.

### **조회할 컬럼**
- **ID**: 개발자의 ID
- **EMAIL**: 개발자의 이메일 주소
- **FIRST NAME**: FIRST NAME
- **LAST NAME**: LAST NAME



---

## 예제 데이터

### SKILLCODES 테이블

개발자들이 사용하는 프로그래밍 언어에 대한 정보를 담고 있는 테이블입니다.

| Column Name | Type    | Nullable | Description        |
|-------------|---------|----------|--------------------|
| NAME        | VARCHAR | FALSE    | 스킬 이름          |
| CATEGORY    | VARCHAR | FALSE    | 스킬 범주          |
| CODE        | INTEGER | FALSE    | 스킬 코드 (2진수)  |

#### 예시 데이터

| NAME        | CATEGORY   | CODE |
|-------------|------------|------|
| C++         | Back End   | 4    |
| JavaScript  | Front End  | 16   |
| Java        | Back End   | 128  |
| Python      | Back End   | 256  |
| C#          | Back End   | 1024 |
| React       | Front End  | 2048 |
| Vue         | Front End  | 8192 |
| Node.js     | Back End   | 16384|

---

### DEVELOPERS 테이블

개발자들의 정보를 담고 있는 테이블입니다.

| Column Name | Type    | Nullable | Description        |
|-------------|---------|----------|--------------------|
| ID          | VARCHAR | FALSE    | 개발자 ID          |
| FIRST_NAME  | VARCHAR | TRUE     | 이름               |
| LAST_NAME   | VARCHAR | TRUE     | 성                 |
| EMAIL       | VARCHAR | FALSE    | 이메일 주소        |
| SKILL_CODE  | INTEGER | FALSE    | 스킬 코드 (2진수)  |

#### 예시 데이터

| ID   | FIRST_NAME | LAST_NAME | EMAIL                           | SKILL_CODE |
|------|------------|-----------|---------------------------------|------------|
| D161 | Carsen     | Garza     | carsen_garza@grepp.co           | 2048       |
| D162 | Cade       | Cunningham| cade_cunningham@grepp.co        | 8452       |
| D165 | Jerami     | Edwards   | jerami_edwards@grepp.co         | 400        |

---

비트와 관련된 부분에 대한 설명은 앞선 SQL 문제를 참고하면 될 것 같습니다. 
[언어별 개발자 분류하기 lv4](https://ggami-da.github.io/2024/11/27/sql-coding-lv4(3)/)

```sql
WITH SKILL_FLAGS AS (
    SELECT 
        d.ID,
        d.EMAIL,
        d.FIRST_NAME,
        d.LAST_NAME,
        BIT_OR(CASE WHEN s.CATEGORY = 'Front End' THEN 1 ELSE 0 END) AS HAS_FRONT_END,
        BIT_OR(CASE WHEN s.NAME = 'Python' THEN 1 ELSE 0 END) AS HAS_PYTHON,
        BIT_OR(CASE WHEN s.NAME = 'C#' THEN 1 ELSE 0 END) AS HAS_CSHARP
    FROM DEVELOPERS d
    JOIN SKILLCODES s
      ON (d.SKILL_CODE & s.CODE) = s.CODE
    GROUP BY 1, 2, 3, 4
)
SELECT ID
     , EMAIL
     , FIRST_NAME
     , LAST_NAME
FROM SKILL_FLAGS
WHERE HAS_FRONT_END = 1
ORDER BY 1 ASC
```


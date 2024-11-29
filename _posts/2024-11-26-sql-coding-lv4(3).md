---
layout: post
title:  "[SQL] Programmers - 언어별 개발자 분류하기 LV4"
date:   2024-11-27
last_modified_at: 2024-11-27
categories: [SQL]
tags: [SQL, Data]
---

{: .box-success}
프로그래머스 SQL 문제를 기반으로 설명합니다.

# 문제 내용

## 문제 설명

주어진 문제는 **SKILLCODES**와 **DEVELOPERS** 테이블을 이용하여 개발자별 스킬 정보에 따라 **등급 (GRADE)**을 매기고, 이를 기준으로 정보를 조회하는 문제입니다.

### **조회할 컬럼**
- **GRADE**: 개발자의 스킬에 따른 등급
- **ID**: 개발자의 ID
- **EMAIL**: 개발자의 이메일 주소

### **등급 기준**
- **A**: Front End 스킬과 Python 스킬을 함께 가진 개발자
- **B**: C# 스킬을 가진 개발자
- **C**: 그 외의 Front End 스킬을 가진 개발자

### **정렬 기준**
- 결과는 **GRADE**와 **ID** 기준으로 오름차순 정렬

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
| D165 | Jerami     | Edwards   | jerami_edwards@grepp.co         | 400        |
| D161 | Carsen     | Garza     | carsen_garza@grepp.co           | 2048       |
| D164 | Kelly      | Grant     | kelly_grant@grepp.co            | 1024       |
| D163 | Luka       | Cory      | luka_cory@grepp.co              | 16384      |
| D162 | Cade       | Cunningham| cade_cunningham@grepp.co        | 8452       |

---

## SQL 쿼리 분석

이 문제를 해결하기 위해서는 개발자의 **SKILL_CODE**와 **SKILLCODES** 테이블의 **CODE** 값을 비교하여 개발자가 가진 스킬을 파악하고, 이를 기준으로 개발자들의 **GRADE**를 결정해야 합니다.

### **STEP 1**: 개발자의 스킬 정보 추출
우리는 `DEVELOPERS` 테이블과 `SKILLCODES` 테이블을 **비트 AND 연산**을 사용하여 결합합니다. 이 때, `SKILL_CODE`와 `CODE` 값을 비교하여 개발자가 해당 스킬을 가지고 있는지 판단합니다.

### **STEP 2**: 개발자의 GRADE 및 성과금 계산
개발자가 가진 스킬에 따라 GRADE를 매기고, 이를 바탕으로 성과금을 계산합니다. GRADE는 **A**, **B**, **C**로 나누며, 해당 GRADE에 맞는 성과금을 계산합니다.

### **SQL 쿼리**

```sql
WITH SKILL_FLAGS AS (
    SELECT 
        d.ID,
        d.EMAIL,
        BIT_OR(CASE WHEN s.CATEGORY = 'Front End' THEN 1 ELSE 0 END) AS HAS_FRONT_END,
        BIT_OR(CASE WHEN s.NAME = 'Python' THEN 1 ELSE 0 END) AS HAS_PYTHON,
        BIT_OR(CASE WHEN s.NAME = 'C#' THEN 1 ELSE 0 END) AS HAS_CSHARP
    FROM DEVELOPERS d
    JOIN SKILLCODES s
      ON (d.SKILL_CODE & s.CODE) = s.CODE
    GROUP BY d.ID, d.EMAIL
)
SELECT 
    CASE
        WHEN HAS_FRONT_END = 1 AND HAS_PYTHON = 1 THEN 'A'
        WHEN HAS_CSHARP = 1 THEN 'B'
        WHEN HAS_FRONT_END = 1 THEN 'C'
    END AS GRADE,
    ID,
    EMAIL
FROM SKILL_FLAGS
WHERE (HAS_FRONT_END = 1 AND HAS_PYTHON = 1)
   OR HAS_CSHARP = 1
   OR (HAS_FRONT_END = 1 AND HAS_PYTHON = 0 AND HAS_CSHARP = 0)
ORDER BY GRADE, ID;
```

## 1. 비트 연산의 기본 개념

**비트 연산**은 숫자들의 비트 단위로 수행되는 연산입니다. SQL에서 비트 연산자는 주로 여러 스킬이나 조건을 하나의 정수값에 압축하여 처리할 때 유용합니다. 이때 중요한 연산자는 **AND**, **OR**, **XOR** 등입니다.

### **비트 AND (`&`) 연산**
- **비트 AND 연산**은 두 숫자에 대해 대응하는 비트가 모두 1일 때만 1을 반환합니다.
- 예를 들어, 2진수 `11001000`과 `10000000`에 대해 AND 연산을 하면, 대응되는 비트가 모두 1인 자리는 1을, 나머지는 0을 반환합니다.

### **비트 OR (`|`) 연산**
- **비트 OR 연산**은 두 숫자에 대해 대응하는 비트 중 하나라도 1일 경우 1을 반환합니다.

## 2. 문제에서의 비트 연산 활용

문제에서 **DEVELOPERS** 테이블의 **SKILL_CODE**와 **SKILLCODES** 테이블의 **CODE** 값은 **비트**로 표현된 정보입니다. 각 스킬은 2진수로 표현된 **2의 제곱수**로 구성되어 있으며, 각 비트는 개발자가 가진 특정 스킬을 나타냅니다.

### **SKILL_CODE**와 **CODE**의 관계
- **DEVELOPERS.SKILL_CODE**는 개발자가 가진 여러 스킬을 **비트** 형태로 표현합니다. 각 비트는 해당 개발자가 가진 특정 스킬을 나타냅니다.
- **SKILLCODES.CODE**는 각 스킬을 **2진수**로 표현한 값입니다. 예를 들어, **React** 스킬의 CODE 값은 `2048`로, 이는 `100000000000` (2진수)로 표현됩니다.
- 이때, **AND 연산**을 사용하여 **DEVELOPERS.SKILL_CODE**와 **SKILLCODES.CODE**를 비교함으로써 개발자가 해당 스킬을 가지고 있는지 확인할 수 있습니다.

## 3. 비트 연산이 적용된 SQL 쿼리 분석

쿼리에서 사용된 주요 부분은 다음과 같습니다:

```sql
ON (d.SKILL_CODE & s.CODE) = s.CODE
```
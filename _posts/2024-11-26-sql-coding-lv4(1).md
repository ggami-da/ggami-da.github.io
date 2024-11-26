---
layout: post
title:  "[SQL] Programmers - 특정 세대의 대장균 찾기 LV4"
date:   2024-11-24
last_modified_at: 2024-11-24
categories: [SQL]
tags: [SQL, Data]
---

{: .box-success}
프로그래머스 SQL 문제를 기반으로 설명합니다.

# SQL 재귀 CTE (`WITH RECURSIVE`)와 계층적 데이터 처리

오늘은 SQL의 `WITH RECURSIVE` 문법을 사용하여 계층적 데이터를 처리하는 방법에 대해 설명드리겠습니다.    
특히, 부모-자식 관계 데이터를 기반으로 **세대(generation)**를 계산하는 방법과 `UNION ALL`의 역할을 알아보겠습니다.  

## 예제 데이터

프로그래머스 LV4 문제의 특정 세대의 대장균 찾기 문제입니다.  
다음은 부모-자식 관계를 나타내는 예제 데이터입니다.  

| ID  | PARENT_ID |
|-----|-----------|
| 1   | NULL      |
| 2   | NULL      |
| 3   | 1         |
| 4   | 2         |
| 5   | 2         |
| 6   | 4         |
| 7   | 3         |
| 8   | 6         |

- `PARENT_ID`가 `NULL`인 경우는 최상위 노드(1세대)입니다.
- 각 ID는 `PARENT_ID`를 참조하여 계층적 구조를 형성합니다.
- 이를 기반으로, 각 ID의 **세대(generation)**를 계산하겠습니다.

## SQL 쿼리

다음은 `WITH RECURSIVE`를 사용하여 계층적 세대를 계산하는 SQL 쿼리입니다.

```sql
WITH RECURSIVE GenerationCTE AS (
    -- 1. Anchor 단계: 최상위 노드 (PARENT_ID가 NULL인 경우)
    SELECT 
        ID,
        PARENT_ID,
        1 AS generation
    FROM your_table
    WHERE PARENT_ID IS NULL

    UNION ALL

    -- 2. Recursive 단계: 부모 세대 + 1로 자식 노드를 계산
    SELECT 
        t.ID,
        t.PARENT_ID,
        g.generation + 1 AS generation
    FROM your_table t
    INNER JOIN GenerationCTE g
        ON t.PARENT_ID = g.ID
)
-- 최종 결과 출력
SELECT 
    ID,
    PARENT_ID,
    generation
FROM GenerationCTE
ORDER BY generation, ID;
```

## 단계별 작동 예시

### 1. Anchor 단계 (초기 값)
`PARENT_ID`가 `NULL`인 레코드들(최상위 노드)을 선택합니다.   
이 레코드는 **1세대**로 설정됩니다.  

**Anchor 단계 결과:**

| ID  | PARENT_ID | Generation |
|-----|-----------|------------|
| 1   | NULL      | 1          |
| 2   | NULL      | 1          |

### 2. 첫 번째 Recursive 단계
Anchor 단계에서 얻은 결과를 바탕으로 자식 노드를 찾습니다.    
각 자식 노드는 부모의 **Generation + 1**로 설정됩니다.  

**첫 번째 Recursive 단계 결과:**

| ID  | PARENT_ID | Generation |
|-----|-----------|------------|
| 3   | 1         | 2          |
| 4   | 2         | 2          |
| 5   | 2         | 2          |

### 3. 두 번째 Recursive 단계
이제, 1세대의 자식들(3, 4, 5)에서 자식 노드를 찾아 **Generation + 1**을 적용합니다.  

**두 번째 Recursive 단계 결과:**

| ID  | PARENT_ID | Generation |
|-----|-----------|------------|
| 6   | 4         | 3          |
| 7   | 3         | 3          |

### 4. 세 번째 Recursive 단계
2세대의 자식들(6, 7)에서 자식 노드를 찾아 **Generation + 1**을 적용합니다.  

**세 번째 Recursive 단계 결과:**

| ID  | PARENT_ID | Generation |
|-----|-----------|------------|
| 8   | 6         | 4          |

## 최종 결과

모든 단계가 완료되면 다음과 같은 결과가 출력됩니다:

| ID  | PARENT_ID | Generation |
|-----|-----------|------------|
| 1   | NULL      | 1          |
| 2   | NULL      | 1          |
| 3   | 1         | 2          |
| 4   | 2         | 2          |
| 5   | 2         | 2          |
| 6   | 4         | 3          |
| 7   | 3         | 3          |
| 8   | 6         | 4          |

## `WITH RECURSIVE`의 작동 원리

### Anchor 단계
- `PARENT_ID`가 `NULL`인 레코드를 가져옵니다.  
- 이를 **1세대**로 설정합니다.  

### Recursive 단계
- 이전 단계에서 얻은 자식 노드를 찾아 `generation + 1`을 적용하여 세대를 계산합니다.  

### 종료 조건
- 자식 노드가 없을 때까지 이 과정을 반복합니다.  

## `UNION ALL`의 역할

`UNION ALL`은 **Anchor 단계와 Recursive 단계의 결과를 결합**하는 역할을 합니다.  

- **Anchor 단계**: 최상위 노드 데이터 가져오기
- **Recursive 단계**: 자식 노드 데이터를 계산
- **UNION ALL**: 이 두 단계의 결과를 **누적**하여 전체 계층 구조를 만듭니다.

### 왜 `UNION ALL`을 사용하는가?
- `UNION ALL`은 중복을 허용하며 모든 데이터를 포함합니다.
- `UNION`은 중복 제거를 위해 추가 연산이 필요하므로 성능이 떨어질 수 있습니다.
- 재귀 CTE에서는 중복된 데이터를 생성할 가능성이 거의 없으므로, `UNION ALL`이 적합합니다.

## 마무리

`WITH RECURSIVE`는 계층적 데이터를 처리하는 데 매우 유용한 도구입니다.    
이를 통해 부모-자식 관계 데이터를 기반으로 **세대 정보**를 계산하거나, 트리 구조를 시각화할 수 있습니다.

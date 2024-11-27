---
layout: post
title:  "[Casual Inference] Graph Model - Blocking, Collider, D-seperate"
date:   2024-11-26
last_modified_at: 2024-11-26
categories: [Casual Inference]
tags: [Casual Inference, Data]
---

{: .box-success}
주로 분석할 때 많이 사용하는 모델 이론을 간략히 정리합니다.


# 그래프 조건부 독립

이번에는 2가지를 알아볼 것입니다.  
첫 번째는 “Block”이며 두 번째는 “collider”가 무엇인지 이해하는 것입니다.

## Blocking

![img.png](../../../../img/graph-model(13).png)

자, 앞선 예제를 다시 보갰습니다.  
여기서 G에 대해서 조건을 준다 (= 즉, 통제한다)고 생각을 해본다면 다음과 같습니다.  

1. 조건을 주기 전에는 A와 B는 서로 의존적이지만 이는 G를 통해 의존적이라는 것을 알 수 있습니다.
2. 따라서 G에 조건을 준다면 해당 경로를 차단하게 되기 때문에 A와 B는 서로 독립이 됩니다.

구체적인 예시를 들어서 설명하기 위해서 **A = 온도**, **G = 보도가 얼었는지 여부**, **B = 누군가 넘어지는 여부**라고 가정합시다.  
- 온도(A)는 보도가 얼었는지 여부(G)에 영향을 줍니다. 예를 들어, 온도가 매우 낮으면 보도가 얼 확률이 높아집니다.
- 보도가 얼었는지 여부(G)는 누군가 넘어지는 여부(B)에 영향을 줍니다. 예를 들어, 보도가 얼어 있다면 누군가 미끄러져 넘어질 가능성이 증가할 것입니다.

이 상황에서 온도(A)가 누군가 넘어지는 여부(B)에 직접적인 영향을 주지는 않습니다.  
즉, 온도 자체가 높거나 낮은 것은 누군가 넘어질지 여부에 직접 영향을 미치지 않고, 보도가 얼었는지 여부(G)를 통해 간접적으로 영향을 미칩니다.  
만약 **G(= 보도가 얼었는지 여부)**를 통제한다고 생각해 봅시다. 여기서 "통제"란 G를 고정한다는 의미입니다. 즉, 보도가 얼었는지 얼지 않았는지 상태를 확정한 상태에서 분석하는 것입니다.  

- 예를 들어, **보도가 얼어 있다고(G=얼음 있음)** 고정한다면:
    - 이 경우 온도(A)는 이미 G(보도의 상태)를 통해 반영되었기 때문에, 온도가 높거나 낮은 것이 누군가 넘어지는지 여부(B)에 영향을 미치지 않습니다.
    - 누군가 넘어지는지 여부는 단순히 G(보도가 얼었는지 여부)에만 의존하게 됩니다.
- 반대로, **보도가 얼지 않았다고(G=얼음 없음)** 고정한다면:
    - 이 경우에도 마찬가지로, 온도(A)는 누군가 넘어지는 여부(B)에 영향을 미치지 않습니다.
    - 보도가 얼지 않은 상황에서는 넘어질 가능성 자체가 거의 없기 때문입니다.

따라서, G를 통제하게 되면 A(온도)와 B(넘어짐)는 서로 독립적으로 변하며, 더 이상 연관이 없어집니다.  
이는 Forks인 경로에서도 동일할 수 있습니다.   
위의 그림에서 E를 통제하게 된다면 A와 B는 서로 연관이 없어집니다. 

## Collider

![img.png](../../../../img/graph-model(11).png)

자, 앞서 Inverted Forks에서 본 것을 본다면 정보는 G에서 충돌하고 있습니다.    
즉, 이미 이러한 상황에서 A와 B는 독립적이기 때문에 차단할 경로가 따로 없습니다.  
하지만 만약 G를 통제하게 된다면 오히려 반대로 A와 B의 연관성을 만들게 됩니다.  

**구체적인 예시를 들어 설명하겠습니다.**

- **A**는 하나의 스위치가 켜져 있는지 여부입니다.
- **B**는 또 다른 스위치가 켜져 있는지 여부입니다.
- **G**는 전구가 켜져 있는지 여부입니다.

여기서 **G(= 전구의 상태)**는 두 스위치 A와 B가 모두 켜져 있을 때만 전구가 켜지는 구조로 되어 있습니다.  
A와 B는 각각 독립적으로 설정된 스위치입니다. 따라서 A가 켜져 있다고 해서 B가 켜져 있는지 여부를 알 수는 없습니다.  

예를 들어:  
- A가 "켜짐" 상태라는 정보는 B가 "켜짐" 또는 "꺼짐" 상태일 가능성을 동일하게 남겨 둡니다.

즉, **A와 B는 서로 독립적**입니다.  
만약 **G = "전구가 켜짐"**이라는 조건을 걸면 상황이 달라집니다.  
전구(G)는 A와 B가 모두 켜져 있을 때만 켜질 수 있으므로, 이제 A와 B는 서로 연관됩니다.  
- 예를 들어, G가 켜져 있다고 하면:
    - 만약 A가 "켜짐" 상태라면, B도 반드시 "켜짐" 상태여야 G가 켜질 수 있습니다.
    - 반대로, A가 "꺼짐" 상태라면, B가 켜져 있더라도 G는 켜질 수 없습니다.

따라서, **G에 조건을 걸면 A와 B는 더 이상 독립적이지 않고, 조건부로 종속적(Conditionally Dependent)**이 됩니다.  

## d-separation

**d-separation**은 방향성 비순환 그래프(Directed Acyclic Graph, DAG)에서 변수들 간의 **독립성**을 판별하기 위한 방법론입니다.  
특정 노드 집합에 조건을 걸었을 때, 두 변수 사이의 정보 흐름(의존성)이 차단되는지 확인하는 데 사용됩니다.  
d-separation은 DAG 내에서 경로를 따라 정보가 전달되는 방식과 이를 차단하는 방법을 정의합니다. 이를 이해하기 위해, 경로 상의 구조와 차단 규칙을 알아야 하고 저희는 이미 이 부분을 확인했습니다.  

1. **체인 (Chain)**
    
    예: A → B → C
    
    - 변수 A가 B를 거쳐 변수 C에 영향을 미치는 구조.
    - **차단 조건**: 중간 변수 B에 조건을 걸면 A와 C의 정보 흐름이 차단됩니다.
    - 즉, **P(C | A, B) = P(C | B)**.
2. **포크 (Fork)**
    
    예: A ← B → C
    
    - 변수 B가 변수 A와 변수 C에 각각 영향을 미치는 구조.
    - **차단 조건**: B에 조건을 걸면 A와 C의 정보 흐름이 차단됩니다.
    - 즉, **P(A, C | B) = P(A | B)P(C | B)**.
3. **충돌점 (Collider)**
    
    예: A → B ← C
    
    - 변수 A와 변수 C가 동시에 변수 B에 영향을 미치는 구조.
    - **차단 조건**: 기본적으로 A와 C는 독립적입니다.하지만 **B 또는 B의 자손에 조건을 걸면**, A와 C 사이의 정보 흐름이 활성화되어 의존성이 생깁니다.

d-separation의 규칙은 다음과 같습니다.   
두 변수 사이의 경로가 차단(d-separated)되기 위해 다음 조건이 충족되어야 합니다:

1. *체인(Chain) 또는 포크(Fork)**의 경우,
    
    중간 노드(예: B)가 조건에 포함되면 경로가 차단됩니다.
    
2. *충돌점(Collider)**의 경우,
    
    충돌점(B) 자체 또는 그 자손에 조건이 없는 경우 경로가 차단됩니다.
    
    충돌점에 조건이 걸리면 경로가 활성화됩니다.
    
3. 특정 노드 집합 C가 주어진 두 변수 간의 **모든 경로를 차단**한다면,
    
    두 변수는 C에 대해 조건부 독립입니다. 이를 **A ⫫ B | C**로 표현합니다.
    

d-separation은 다음과 같은 분야에서 활용됩니다:

- **인과 추론(Causal Inference):** 변수들 간의 인과 관계를 이해하고, 특정 조건에서 독립성을 보장합니다.
- **통계 모델링:** 조건부 독립성 가정을 확인하여 올바른 모델을 설계할 수 있습니다.
- **무시 가능성(Ignorability) 가정:** 잠재적 결과와 처치 변수 사이의 독립성을 확인합니다. 예: Y0,Y1⊥⊥A∣X.
    
    Y0,Y1⊥ ⁣ ⁣ ⁣⊥A∣XY^0, Y^1 \perp\!\!\!\perp A | X


## Ref
- https://www.coursera.org/learn/crash-course-in-causality
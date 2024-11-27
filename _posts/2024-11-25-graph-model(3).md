---
layout: post
title:  "[Casual Inference] Graph Model - DAG 경로별 종류"
date:   2024-11-26
last_modified_at: 2024-11-26
categories: [Casual Inference]
tags: [Casual Inference, Data]
---

{: .box-success}
주로 분석할 때 많이 사용하는 모델 이론을 간략히 정리합니다.


# 그래프 경로에 따른 종류

경로에 따른 종류는 크게 3가지로 볼 수 있습니다.

1. Forks 
2. Chains (like a chain reaction)
3. Inverted forks

## Forks

![img.png](../../../../img/graph-model(7).png)

포크처럼 생겼다고 해서 Forks라고 부릅니다.  
그림을 보면 E가 D와 F 두 변수에 영향을 주고 있음을 알 수 있습니다.  
앞으로 이러한 그림과 같은 경로는 Forks라고 부르고자 합니다.  

![img.png](../../../../img/graph-model(8).png)

이를  조금 더 복잡하게 본다면 다음과 같습니다.  
여전히 정보는 E에서 A, B로 흐르고 있음을 알 수 있습니다. 즉 긴 Forks 입니다.  
E는 A와 B 양쪽에 영향을 주는 것이 분명하기 때문에 결국엔 A와 B는 이 경로를 통해서 서로 종속적임을 알 수 있습니다. 

## Chain

![img.png](../../../../img/graph-model(9).png)

체인은 본질적으로 연쇄 반응과 같다고 생각할 수 있습니다.  
그림을 보면 D가 E에 영향을 주고 E가 F에 영향을 주고 있습니다. 즉 모든 영향이 하나의 방향으로 흐르고 있습니다.  

![img.png](../../../../img/graph-model(10).png)

이를 조금 더 복잡하게 보더라도 동일하게 해석할 수 있습니다.  
A가 G에 영향을 주고 G가 D에 영향을 주고 D가 F에 영향을 주고 F가 B에 영향을 주는 것을 볼 수 있습니다.  
즉 A와 B는 서로 연관되어 있습니다.  

## Inverted Forks

![img.png](../../../../img/graph-model(11).png)

Forks와 비슷하지만 화살표가 반대 방향인 것을 알 수 있습니다.  
이를 역순된 Forks라고 합니다. 위의 그림을 본다면 A와 B 둘 다 G에 영향을 주고 있습니다.  
A와 B에서 나온 정보가 G에서 충돌(= G를 “collider”)하는 것을 볼 수 있습니다.   
G에서 A 또는 B로의 정보 흐름이 없습니다. 따라서 A와 B는 이 경로에선 독립적입니다.  

![img.png](../../../../img/graph-model(12).png)

조금 더 복잡하게 보더라도 이 경우 G에서 충돌되는 것을 알 수 있습니다.  
모든 정보가 G에서 중단이 되게 때문에 A와 B는 독립적입니다.  
결과적으로 노드 간의 관계를 파악할 때 이처럼 어떤 경로인지를 살펴봐야합니다.  

가장 중요한 것은 그 경로 중 어디에 충돌체가 있는지, 정보가 한 노드에서 다른 노드로 흐르는 것을 방해하는 충돌체가 있는지 입니다. 이러한 지식을 활용하여 인과추론 시 어떤 변수를 제어해야 하는지 식별하는 데 중요한 힌트를 얻을 수 있습니다.

## Ref
- https://www.coursera.org/learn/crash-course-in-causality
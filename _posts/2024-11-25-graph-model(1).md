---
layout: post
title:  "[Casual Inference] Graph Model 기초"
date:   2024-11-23
last_modified_at: 2024-11-23
categories: [Casual Inference]
tags: [Casual Inference, Data]
---

{: .box-success}
주로 분석할 때 많이 사용하는 모델 이론을 간략히 정리합니다.


# 그래프 이론

그래프 모델은 인과 추론에 굉장히 유용합니다.  
그래프 모델은 어떤 변수를 통제해야 하는지 식별하는데 도움을 주고 분석에 가정을 명확히 만들어 줄 수 있기 때문에 인과 관계를 알려주는 기본 언어 중 하나라고 생각해볼 수 있습니다.  
⇒ 즉 그래프 모델은 인과 관계에 대한 생각을 명확히 표현할 수 있도록 합니다. 

{: .box-success}
인과 그래프는 DAG(**`directed acyclic graphs (순환 없는 방향 그래프)`)라고 부르기도 합니다. 이는 방향이 있지만 순환은 없는 그래프를 의미하며 순환은 시작 노드로 다시 돌아오는 경우를 말합니다.

# 그래프 이론 기초 용어

기본적으로 그래프(graph)는 다음 그림처럼 **`노드(node, vertex)`**와 그 사이를 잇는 **`간선 혹은 링크(edge)`**으로 이루어진 구조를 말합니다.

![img.png](../../../../img/graph-model(1).png)

아래를 보면 A에서 B로 화살표가 존재하는 것을 볼 수 있는데 이는 A가 B에 영향을 미치는 것으로 생각할 수 있습니다.  
화살표 때문에 이것은 방향 그래프 (directed graph)라고 합니다.  

$$
A \rightarrow B
$$

아래를 보면 A에서 B로 화살표가 없는 것을 볼 수 있는데 이는 무방향 그래프 (undirected graph)라고 합니다.  
기본적으로 A와 B는 연관은 있지만 인과적인 방향성은 알 수 없음을 의미합니다.  

$$
A- B
$$

그래픽 모델에서 한 가지 주요하게 생각해야할 점은 모든 그래프가 변수 간의 관계에 대한 가정을 나타낸다는 것입니다.  
따라서 변수들이 서로 독립적인지, 어떤 변수에 의존하는지, 조건부 독립성이 있는지 등을 알려주게 되고 이를 통해서 인과 효과를 도출하는데 사용 해볼 수 있습니다.  
앞서 인과 추론의 그래프 모형은 DAG (**`directed acyclic graphs`**)라고 한다고 했습니다.  
아래처럼 1) 방향이 없거나 2) 순환되는 그래프를 제외한 그래프를 DAG라고 볼 수 있습니다.  

![image.png](../../../../img/graph-model(2).png)

# DAG만 살펴보기

DAG에서 주요하게 알아야 할 용어를 소개하겠습니다.

- 부모 (Parent)
- 자식(child)
- 조상(ancestor)
- 후손(descendant)

```python
import matplotlib.pyplot as plt
import networkx as nx

G = nx.DiGraph()
G.add_edges_from([("A", "Z"), ("Z", "D"), ("B", "D"), ("Z", "B")])

pos = {
    "A": (0, 1),
    "Z": (1, 1),
    "D": (2, 1),
    "B": (1, 0)
}
plt.figure(figsize=(6, 4))
nx.draw(
    G, pos, with_labels=True, arrows=True,
    node_color='skyblue', edge_color='darkred', font_size=12,
    node_size=2000, arrowstyle='-|>', arrowsize=20
)
plt.title("Directed Graph Representation", fontsize=14)
plt.show()


![image.png](../../../../img/graph-model(3).png)

첫 번째, A는 Z의 부모(parent)입니다.  
화살표가 A → Z로 향하는 것을 볼 수 있는데 이는 A가 Z를 발생시키거나 영향을 미치는 것으로 생각할 수 있습니다.  
즉, A가 먼저 일어나고 Z에 영향을 미치기 때문에 A를 Z의 부모라고 생각할 수 있습니다.  
두 번째, B는 Z의 자식(child)입니다.  
세 번째, D는 A의 후손(descendant)입니다.  
그 이유는 A는 Z의 부모이고 Z는 D의 부모이기 때문입니다.  
네 번째, Z는 D의 조상(ancestor)입니다.  
조상이면서 부모가 될 수 있습니다.   

{: .box-success}
- **부모**: 바로 다음에 연결된 노드입니다. 즉, 직접적인 상위 관계를 나타냅니다.
- **조상**: 부모를 포함하여 여러 단계를 통해 연결될 수 있는 상위 노드 전체를 말합니다.


DAG에서는 부모는 하나 이상일 수 있다는 점을 알아야 합니다.  
위의 그림에서 D는 Z와 B라는 부모를 가지고 있습니다. 부모는 2개가 아니라 N개 일 수 있으며 특정 변수 또는 노드는 둘 이상의 부모를 가질 수 있습니다.   
이렇게 DAG는 변수간 관계를 볼 수 있고 어떤 변수를 통제하면 좋을지에 대한 힌트를 줍니다.   
상세한 내용은 다음 챕터에서 확인해보도록 하겠습니다.  
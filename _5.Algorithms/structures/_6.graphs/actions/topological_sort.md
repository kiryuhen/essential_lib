**Топологическая сортировка**

## 1. Описание
Построение линейного упорядочения вершин ориентированного ациклического графа (DAG), чтобы для каждого ребра (u → v) u предшествовала v.

## 2. Сложность
- **Время:** O(V + E)
- **Память:** O(V + E)

## 3. Псевдокод (алгоритм Кана)
```text
Функция topological_sort():
    indegree = массив степеней входа для всех вершин
    Q = очередь вершин с indegree == 0
    res = пустой список
    пока Q не пуста:
        u = Q.dequeue()
        res.append(u)
        для каждого v в Adj[u]:
            indegree[v] -= 1
            если indegree[v] == 0: Q.enqueue(v)
    если длина res < V: вернуть "Есть цикл"
    вернуть res
```

## 4. Реализация на Python
```python
from collections import deque
from typing import Dict, List

def topological_sort(adj: Dict[int, List[int]]) -> List[int] | str:
    V = len(adj)
    indegree = {u: 0 for u in adj}
    for u in adj:
        for v in adj[u]:
            indegree[v] = indegree.get(v, 0) + 1
    queue = deque([u for u in adj if indegree[u] == 0])
    res: List[int] = []
    while queue:
        u = queue.popleft()
        res.append(u)
        for v in adj.get(u, []):
            indegree[v] -= 1
            if indegree[v] == 0:
                queue.append(v)
    return res if len(res) == V else "Graph has cycle"
```


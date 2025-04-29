**Алгоритм Прима**

## 1. Описание
Жадный алгоритм построения минимального остовного дерева связного взвешенного графа, добавляющий на каждом шаге минимальное ребро, ведущие из уже растущего дерева.

## 2. Сложность
- **Время:** O(E log V) при использовании приоритетной очереди.
- **Память:** O(V + E)

## 3. Псевдокод
```text
Функция prim(start):
    MST = пустой список ребер
    visited = {start}
    Q = приоритетная очередь всех ребер (start, v, w)
    пока Q не пуста и размер MST < V-1:
        (u, v, w) = Q.extract_min()
        если v не в visited:
            visited.add(v)
            MST.append((u, v, w))
            для каждого (v, x, w2) в Adj[v]:
                если x не в visited:
                    Q.insert((v, x, w2))
    вернуть MST
```

## 4. Реализация на Python
```python
import heapq
from typing import Dict, List, Tuple

def prim(adj: Dict[int, List[Tuple[int, float]]], start: int) -> List[Tuple[int, int, float]]:
    visited = {start}
    edges: List[Tuple[float, int, int]] = []
    for v, w in adj[start]:
        heapq.heappush(edges, (w, start, v))
    mst: List[Tuple[int, int, float]] = []
    while edges and len(visited) < len(adj):
        w, u, v = heapq.heappop(edges)
        if v in visited:
            continue
        visited.add(v)
        mst.append((u, v, w))
        for x, w2 in adj[v]:
            if x not in visited:
                heapq.heappush(edges, (w2, v, x))
    return mst
```


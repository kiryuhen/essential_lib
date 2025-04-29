**Алгоритм Дейкстры**

## 1. Описание
Итеративный алгоритм нахождения кратчайших путей от одной исходной вершины до всех остальных в связном взвешенном графе без отрицательных весов.

## 2. Сложность
- **Время:** O((V + E) log V) при использовании приоритетной очереди.
- **Память:** O(V + E)

## 3. Псевдокод
```text
Функция dijkstra(src):
    dist = массив размера V со значениями inf
    dist[src] = 0
    Q = приоритетная очередь; Q.insert((0, src))
    пока Q не пуста:
        (d, u) = Q.extract_min()
        если d > dist[u]: продолжить
        для каждого (v, w) в Adj[u]:
            если dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                Q.insert((dist[v], v))
    вернуть dist
```

## 4. Реализация на Python
```python
import heapq
from typing import Dict, List

def dijkstra(adj: Dict[int, List[tuple[int, float]]], src: int) -> List[float]:
    V = len(adj)
    dist = [float('inf')] * V
    dist[src] = 0
    pq: List[tuple[float, int]] = [(0, src)]
    while pq:
        d, u = heapq.heappop(pq)
        if d > dist[u]:
            continue
        for v, w in adj[u]:
            nd = d + w
            if nd < dist[v]:
                dist[v] = nd
                heapq.heappush(pq, (nd, v))
    return dist
```


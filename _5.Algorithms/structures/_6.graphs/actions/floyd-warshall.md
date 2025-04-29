**Алгоритм Флойда–Уоршелла**

## 1. Описание
Динамический алгоритм нахождения кратчайших путей между всеми парами вершин взвешенного графа.

## 2. Сложность
- **Время:** O(V³)
- **Память:** O(V²)

## 3. Псевдокод
```text
Функция floyd_warshall(dist):
    // dist[i][j] — начальное расстояние (inf, если ребра нет)
    для k от 0 до V-1:
        для i от 0 до V-1:
            для j от 0 до V-1:
                если dist[i][k] + dist[k][j] < dist[i][j]:
                    dist[i][j] = dist[i][k] + dist[k][j]
    вернуть dist
```

## 4. Реализация на Python
```python
from typing import List

def floyd_warshall(dist: List[List[float]]) -> List[List[float]]:
    V = len(dist)
    for k in range(V):
        for i in range(V):
            for j in range(V):
                if dist[i][k] + dist[k][j] < dist[i][j]:
                    dist[i][j] = dist[i][k] + dist[k][j]
    return dist
```


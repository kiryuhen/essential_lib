**Алгоритм Беллмана–Форда**

## 1. Описание
Алгоритм нахождения кратчайших путей от одной исходной вершины до всех остальных в графе с возможными отрицательными весами, выявляет также наличие отрицательных циклов.

## 2. Сложность
- **Время:** O(V · E)
- **Память:** O(V + E)

## 3. Псевдокод
```text
Функция bellman_ford(src):
    dist = массив размера V со значениями inf
    dist[src] = 0
    для i от 1 до V-1:
        для каждого ребра (u, v, w) в E:
            если dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
    // проверка отрицательных циклов
    для каждого ребра (u, v, w) в E:
        если dist[u] + w < dist[v]:
            вернуть "Отрицательный цикл обнаружен"
    вернуть dist
```

## 4. Реализация на Python
```python
from typing import List, Tuple, Union

def bellman_ford(V: int, edges: List[Tuple[int, int, float]], src: int) -> Union[List[float], str]:
    dist = [float('inf')] * V
    dist[src] = 0
    for _ in range(V - 1):
        for u, v, w in edges:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
    # проверка на отрицательный цикл
    for u, v, w in edges:
        if dist[u] + w < dist[v]:
            return "Negative weight cycle detected"
    return dist
```


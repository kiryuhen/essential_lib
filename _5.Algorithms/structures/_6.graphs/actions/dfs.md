**Обход в глубину (DFS) в графе**

## 1. Описание
Рекурсивный алгоритм обхода графа, посещающий все достижимые вершины из заданной стартовой, углубляясь по каждому соседу до конца.

## 2. Сложность
- **Время:** O(V + E), где V — число вершин, E — число рёбер.
- **Память:** O(V) — стек рекурсии и массив посещённых.

## 3. Псевдокод
```text
Функция dfs(u):
    пометить u как посещённую
    для каждого v из Adj[u]:
        если v не посещён:
            dfs(v)
```

## 4. Реализация на Python
```python
from typing import Dict, List, Set

def dfs(adj: Dict[int, List[int]], start: int, visited: Set[int] = None) -> None:
    if visited is None:
        visited = set()
    visited.add(start)
    for v in adj.get(start, []):
        if v not in visited:
            dfs(adj, v, visited)
```


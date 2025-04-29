**Обход в ширину (BFS) в графе**

## 1. Описание
Итеративный алгоритм обхода графа по уровням, используя очередь, посещая сначала все соседние вершины, затем переходя на следующий уровень.

## 2. Сложность
- **Время:** O(V + E)
- **Память:** O(V) — очередь и массив посещённых.

## 3. Псевдокод
```text
Функция bfs(start):
    создать пустую очередь Q
    создать пустой набор visited
    Q.enqueue(start)
    visited.add(start)
    пока Q не пуста:
        u = Q.dequeue()
        для каждого v из Adj[u]:
            если v не в visited:
                visited.add(v)
                Q.enqueue(v)
```

## 4. Реализация на Python
```python
from collections import deque
from typing import Dict, List, Set

def bfs(adj: Dict[int, List[int]], start: int) -> List[int]:
    visited: Set[int] = {start}
    queue = deque([start])
    order: List[int] = []
    while queue:
        u = queue.popleft()
        order.append(u)
        for v in adj.get(u, []):
            if v not in visited:
                visited.add(v)
                queue.append(v)
    return order
```


**Обход в ширину (BFS)**

## 1. Описание
Алгоритм обхода графа или дерева по уровням, начиная с корня (или заданной начальной вершины) и переходя на следующий уровень после полного обхода текущего. Для хранения вершин текущего уровня используется очередь.

## 2. Сложность
- **Время:** O(V + E), где V — число вершин, E — число рёбер.
- **Память:** O(V) — очередь и массив отметок посещённых вершин.

## 3. Псевдокод
```text
Функция bfs(start):
    создать пустую очередь Q
    создать пустое множество visited
    Q.enqueue(start)
    visited.add(start)
    пока Q не пуста:
        u = Q.dequeue()
        обработать u
        для каждого соседа v из Adj[u]:
            если v не в visited:
                Q.enqueue(v)
                visited.add(v)
```

## 4. Реализация на Python
```python
from collections import deque
from typing import Dict, List, Set

def bfs(adj: Dict[int, List[int]], start: int) -> List[int]:
    """
    Выполняет обход в ширину на графе.
    Возвращает порядок обхода вершин.
    """
    visited: Set[int] = set([start])
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


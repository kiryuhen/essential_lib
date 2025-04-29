**Обход в глубину (DFS) через явный стек**

## 1. Описание
Рекурсивный обход графа или дерева в глубину можно заменить итеративным с использованием стека для хранения узлов.

## 2. Сложность
- **Время:** O(V + E) для графов (V — число вершин, E — число рёбер).
- **Память:** O(V) — стек для хранения узлов.

## 3. Псевдокод
```text
Функция dfs_iterative(start):
    создать пустой стек
    создать пустое множество visited
    стек.push(start)
    пока стек не пуст:
        node = стек.pop()
        если node не в visited:
            отметить node как посещённый
            для каждого соседа v из Adj[node]:
                если v не в visited:
                    стек.push(v)
```

## 4. Реализация на Python
```python
from typing import List, Set, Dict

def dfs_iterative(adj: Dict[int, List[int]], start: int) -> List[int]:
    """
    Итеративный DFS на графе, заданном списком смежности.
    Возвращает порядок обхода.
    """
    visited: Set[int] = set()
    stack: List[int] = [start]
    order: List[int] = []
    while stack:
        node = stack.pop()
        if node not in visited:
            visited.add(node)
            order.append(node)
            for v in adj.get(node, []):
                if v not in visited:
                    stack.append(v)
    return order
```


**Поиск сильносвязных компонент (Kosaraju)**

## 1. Описание
Алгоритм находит все сильно связные компоненты в ориентированном графе за два прохода DFS: сначала на исходном графе, записывая порядок завершения, затем на транспонированном, выбирая вершины в обратном порядке завершения.

## 2. Сложность
- **Время:** O(V + E)
- **Память:** O(V + E)

## 3. Псевдокод
```text
Функция kosaraju():
    visited = пустой набор
    stack = пустой стек
    // 1-й проход
    для каждой вершины u:
        если u не в visited: dfs1(u)
    // транспонирование графа
    transposed = транспонировать(adj)
    visited.clear()
    scc_list = []
    // 2-й проход
    пока stack не пуст:
        u = stack.pop()
        если u не в visited:
            component = []
            dfs2(transposed, u, component)
            scc_list.append(component)
    вернуть scc_list

Процедуры:
Функция dfs1(u):
    visited.add(u)
    для v в Adj[u]:
        если v не в visited: dfs1(v)
    stack.push(u)

Функция dfs2(u, component):
    visited.add(u)
    component.append(u)
    для v в Transposed[u]:
        если v не в visited: dfs2(v, component)
```

## 4. Реализация на Python
```python
from typing import Dict, List, Set

def kosaraju(adj: Dict[int, List[int]]) -> List[List[int]]:
    V = len(adj)
    visited: Set[int] = set()
    stack: List[int] = []

    def dfs1(u: int):
        visited.add(u)
        for v in adj.get(u, []):
            if v not in visited:
                dfs1(v)
        stack.append(u)

    for u in adj:
        if u not in visited:
            dfs1(u)

    # транспонирование
    transposed: Dict[int, List[int]] = {u: [] for u in adj}
    for u in adj:
        for v in adj[u]:
            transposed[v].append(u)

    visited.clear()
    scc_list: List[List[int]] = []

    def dfs2(u: int, comp: List[int]):
        visited.add(u)
        comp.append(u)
        for v in transposed.get(u, []):
            if v not in visited:
                dfs2(v, comp)

    while stack:
        u = stack.pop()
        if u not in visited:
            component: List[int] = []
            dfs2(u, component)
            scc_list.append(component)
    return scc_list
```


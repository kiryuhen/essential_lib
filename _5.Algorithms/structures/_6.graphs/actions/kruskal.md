**Алгоритм Краскала**

## 1. Описание
Жадный алгоритм построения минимального остовного дерева, сортирующий все ребра по весу и добавляющий их по возрастанию, если они соединяют разные компоненты.

## 2. Сложность
- **Время:** O(E log E) ≈ O(E log V)
- **Память:** O(V + E)

## 3. Псевдокод
```text
Функция kruskal():
    MST = пустой список ребер
    sort edges по возрастанию веса
    DisjointSet DSU
    для каждого ребра (u, v, w) в sorted edges:
        если DSU.find(u) != DSU.find(v):
            DSU.union(u, v)
            MST.append((u, v, w))
    вернуть MST
```

## 4. Реализация на Python
```python
from typing import List, Tuple

class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        rx, ry = self.find(x), self.find(y)
        if rx == ry:
            return False
        if self.rank[rx] < self.rank[ry]:
            self.parent[rx] = ry
        elif self.rank[rx] > self.rank[ry]:
            self.parent[ry] = rx
        else:
            self.parent[ry] = rx
            self.rank[rx] += 1
        return True

def kruskal(n: int, edges: List[Tuple[int, int, float]]) -> List[Tuple[int, int, float]]:
    ds = DSU(n)
    mst = []
    for u, v, w in sorted(edges, key=lambda x: x[2]):
        if ds.union(u, v):
            mst.append((u, v, w))
    return mst
```


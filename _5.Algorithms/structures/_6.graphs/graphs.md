**Графы**

## 1. Описание
Граф — абстрактная структура данных, состоящая из набора вершин (V) и множества рёбер (E), соединяющих пары вершин. Может быть ориентированным или неориентированным.

**Цель и применение:**
- Моделирование сетей: дорожных, социальных, коммуникационных и т. д.
- Задачи маршрутизации, поиска связности, анализа потоков.

## 2. Представления
- **Список смежности**: для каждой вершины хранится список соседей. Эффективно для разреженных графов.
- **Матрица смежности**: матрица размера |V|×|V|, элемент 1 указывает наличие ребра. Простая, но затратна по памяти для больших графов.

## 3. Алгоритмы и их сложности
### 3.1 Поиск
- **DFS (глубинный обход)**: O(V + E)
- **BFS (ширинный обход)**: O(V + E)

### 3.2 Кратчайшие пути
- **Дейкстра**: O((V + E) log V)
- **Беллман–Форд**: O(VE)
- **Флойд–Уоршелл**: O(V³)

### 3.3 Минимальное остовное дерево
- **Прим**: O(E log V)
- **Краскал**: O(E log E)

### 3.4 Другие
- **Топологическая сортировка**: O(V + E)
- **Поиск сильносвязных компонент** (Косарайджу или Тарьяна): O(V + E)

## 4. Псевдокод ключевых алгоритмов
```text
// DFS
Функция DFS(u):
    пометить u как посещённую
    для каждого v в Adj[u]:
        если v не посещён:
            DFS(v)

// Dijkstra (используя приоритетную очередь)
Функция Dijkstra(src):
    dist = массив длины |V| со значениями inf
    dist[src] = 0
    Q = приоритетная очередь; Q.insert((0, src))
    пока Q не пуста:
        (d, u) = Q.extract_min()
        если d > dist[u]: продолжить
        для каждого (u, v) с весом w:
            если dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                Q.insert((dist[v], v))
```

## 5. Реализация на Python
```python
import heapq

class Graph:
    def __init__(self, vertices):
        self.V = vertices
        self.adj = {i: [] for i in range(vertices)}

    def add_edge(self, u, v, weight=1, directed=False):
        self.adj[u].append((v, weight))
        if not directed:
            self.adj[v].append((u, weight))

    def dfs(self, start, visited=None):
        if visited is None:
            visited = set()
        visited.add(start)
        for (v, _) in self.adj[start]:
            if v not in visited:
                self.dfs(v, visited)
        return visited

    def bfs(self, start):
        visited = set([start])
        queue = [start]
        order = []
        while queue:
            u = queue.pop(0)
            order.append(u)
            for (v, _) in self.adj[u]:
                if v not in visited:
                    visited.add(v)
                    queue.append(v)
        return order

    def dijkstra(self, src):
        dist = [float('inf')] * self.V
        dist[src] = 0
        pq = [(0, src)]
        while pq:
            d, u = heapq.heappop(pq)
            if d > dist[u]:
                continue
            for v, w in self.adj[u]:
                if dist[u] + w < dist[v]:
                    dist[v] = dist[u] + w
                    heapq.heappush(pq, (dist[v], v))
        return dist
```


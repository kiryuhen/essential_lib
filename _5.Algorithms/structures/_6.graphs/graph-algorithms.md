# Алгоритмы на графах

Графы — это гибкие и мощные структуры данных, которые используются для представления отношений между объектами. Они находят широкое применение в различных областях: от моделирования социальных сетей и транспортных маршрутов до оптимизации компьютерных сетей и анализа зависимостей в программном обеспечении. Алгоритмы на графах позволяют решать множество практических задач, таких как поиск кратчайшего пути, определение минимального остовного дерева, топологическая сортировка и многие другие.

## Основы теории графов

### Определение и терминология

**Граф** — это упорядоченная пара G = (V, E), где V — множество вершин (или узлов), а E — множество ребер, соединяющих эти вершины.

Ключевые понятия, связанные с графами:

- **Вершина (Vertex/Node)** — основной элемент графа.
- **Ребро (Edge)** — связь между двумя вершинами.
- **Смежность (Adjacency)** — две вершины считаются смежными, если они соединены ребром.
- **Путь (Path)** — последовательность вершин, где каждая последующая вершина соединена ребром с предыдущей.
- **Цикл (Cycle)** — путь, в котором начальная и конечная вершины совпадают.
- **Степень вершины (Degree)** — количество ребер, инцидентных данной вершине.
- **Вес (Weight)** — числовое значение, связанное с ребром (для взвешенных графов).

### Виды графов

1. **Ориентированный граф (Directed Graph или Digraph)** — граф, в котором ребра имеют направление.
2. **Неориентированный граф (Undirected Graph)** — граф, в котором ребра не имеют направления.
3. **Взвешенный граф (Weighted Graph)** — граф, в котором каждому ребру присвоен вес или стоимость.
4. **Циклический граф (Cyclic Graph)** — граф, содержащий хотя бы один цикл.
5. **Ациклический граф (Acyclic Graph)** — граф без циклов.
6. **Связный граф (Connected Graph)** — граф, в котором существует путь между любыми двумя вершинами.
7. **Несвязный граф (Disconnected Graph)** — граф, в котором существуют вершины, не связанные путем.
8. **Полный граф (Complete Graph)** — граф, в котором каждая вершина соединена с каждой другой вершиной.
9. **Двудольный граф (Bipartite Graph)** — граф, вершины которого можно разделить на две группы так, что ребра соединяют только вершины из разных групп.
10. **Планарный граф (Planar Graph)** — граф, который можно изобразить на плоскости без пересечения ребер.

## Представление графов

Существует несколько способов представления графов в памяти компьютера, каждый из которых имеет свои преимущества и недостатки в зависимости от задачи и характеристик графа.

### 1. Матрица смежности (Adjacency Matrix)

Матрица смежности — это двумерный массив размера |V|×|V|, где |V| — количество вершин в графе. Элемент matrix[i][j] равен 1 (или весу ребра), если существует ребро из вершины i в вершину j, и 0 в противном случае.

```python
class Graph:
    """
    Представление графа с помощью матрицы смежности.
    """
    def __init__(self, num_vertices, directed=False):
        """
        Инициализирует граф с заданным количеством вершин.
        
        Args:
            num_vertices: Количество вершин в графе
            directed: Флаг, указывающий, ориентированный ли граф
        """
        self.num_vertices = num_vertices
        self.directed = directed
        # Инициализируем матрицу смежности нулями
        self.matrix = [[0 for _ in range(num_vertices)] for _ in range(num_vertices)]
    
    def add_edge(self, v1, v2, weight=1):
        """
        Добавляет ребро между вершинами v1 и v2.
        
        Args:
            v1: Начальная вершина
            v2: Конечная вершина
            weight: Вес ребра (по умолчанию 1)
        """
        if v1 >= self.num_vertices or v2 >= self.num_vertices or v1 < 0 or v2 < 0:
            raise ValueError("Вершины должны быть в диапазоне 0...num_vertices-1")
        
        self.matrix[v1][v2] = weight
        
        # Если граф неориентированный, добавляем ребро в обратном направлении
        if not self.directed:
            self.matrix[v2][v1] = weight
    
    def remove_edge(self, v1, v2):
        """
        Удаляет ребро между вершинами v1 и v2.
        
        Args:
            v1: Начальная вершина
            v2: Конечная вершина
        """
        if v1 >= self.num_vertices or v2 >= self.num_vertices or v1 < 0 or v2 < 0:
            raise ValueError("Вершины должны быть в диапазоне 0...num_vertices-1")
        
        self.matrix[v1][v2] = 0
        
        # Если граф неориентированный, удаляем ребро в обратном направлении
        if not self.directed:
            self.matrix[v2][v1] = 0
    
    def get_edge(self, v1, v2):
        """
        Возвращает вес ребра между вершинами v1 и v2.
        
        Args:
            v1: Начальная вершина
            v2: Конечная вершина
            
        Returns:
            Вес ребра или 0, если ребра нет
        """
        if v1 >= self.num_vertices or v2 >= self.num_vertices or v1 < 0 or v2 < 0:
            raise ValueError("Вершины должны быть в диапазоне 0...num_vertices-1")
        
        return self.matrix[v1][v2]
    
    def get_neighbors(self, vertex):
        """
        Возвращает список соседей вершины.
        
        Args:
            vertex: Вершина
            
        Returns:
            Список соседних вершин
        """
        if vertex >= self.num_vertices or vertex < 0:
            raise ValueError("Вершина должна быть в диапазоне 0...num_vertices-1")
        
        neighbors = []
        
        for i in range(self.num_vertices):
            if self.matrix[vertex][i] != 0:
                neighbors.append((i, self.matrix[vertex][i]))  # (вершина, вес)
        
        return neighbors
    
    def print_graph(self):
        """
        Выводит граф в виде матрицы смежности.
        """
        for row in self.matrix:
            print(row)

# Пример использования
print("Пример матрицы смежности:")
graph = Graph(5)
graph.add_edge(0, 1)
graph.add_edge(0, 4)
graph.add_edge(1, 2)
graph.add_edge(1, 3)
graph.add_edge(1, 4)
graph.add_edge(2, 3)
graph.add_edge(3, 4)

graph.print_graph()
print(f"Соседи вершины 1: {graph.get_neighbors(1)}")
```

**Преимущества матрицы смежности:**
- Простота реализации
- Быстрая проверка наличия ребра между двумя вершинами (O(1))
- Удобство для представления плотных графов

**Недостатки матрицы смежности:**
- Высокая пространственная сложность (O(V²))
- Неэффективность для разреженных графов
- Неэффективность при добавлении/удалении вершин

### 2. Список смежности (Adjacency List)

Список смежности — это массив (или словарь) списков, где для каждой вершины хранится список ее соседей.

```python
class Graph:
    """
    Представление графа с помощью списка смежности.
    """
    def __init__(self, directed=False):
        """
        Инициализирует пустой граф.
        
        Args:
            directed: Флаг, указывающий, ориентированный ли граф
        """
        self.directed = directed
        self.adj_list = {}  # словарь списков смежности
        self.vertices = set()  # множество всех вершин
    
    def add_vertex(self, vertex):
        """
        Добавляет вершину в граф.
        
        Args:
            vertex: Вершина для добавления
        """
        if vertex not in self.adj_list:
            self.adj_list[vertex] = []
            self.vertices.add(vertex)
    
    def add_edge(self, v1, v2, weight=1):
        """
        Добавляет ребро между вершинами v1 и v2.
        
        Args:
            v1: Начальная вершина
            v2: Конечная вершина
            weight: Вес ребра (по умолчанию 1)
        """
        # Добавляем вершины, если они не существуют
        self.add_vertex(v1)
        self.add_vertex(v2)
        
        # Добавляем ребро (v1, v2)
        self.adj_list[v1].append((v2, weight))
        
        # Если граф неориентированный, добавляем ребро (v2, v1)
        if not self.directed:
            self.adj_list[v2].append((v1, weight))
    
    def remove_edge(self, v1, v2):
        """
        Удаляет ребро между вершинами v1 и v2.
        
        Args:
            v1: Начальная вершина
            v2: Конечная вершина
        """
        if v1 in self.adj_list and v2 in self.vertices:
            self.adj_list[v1] = [(v, w) for v, w in self.adj_list[v1] if v != v2]
            
            # Если граф неориентированный, удаляем ребро (v2, v1)
            if not self.directed and v2 in self.adj_list:
                self.adj_list[v2] = [(v, w) for v, w in self.adj_list[v2] if v != v1]
    
    def remove_vertex(self, vertex):
        """
        Удаляет вершину и все связанные с ней ребра.
        
        Args:
            vertex: Вершина для удаления
        """
        if vertex in self.vertices:
            # Удаляем вершину из множества вершин и списка смежности
            self.vertices.remove(vertex)
            del self.adj_list[vertex]
            
            # Удаляем все ребра, входящие в эту вершину
            for v in self.vertices:
                self.adj_list[v] = [(u, w) for u, w in self.adj_list[v] if u != vertex]
    
    def get_neighbors(self, vertex):
        """
        Возвращает список соседей вершины.
        
        Args:
            vertex: Вершина
            
        Returns:
            Список соседних вершин с весами ребер
        """
        if vertex in self.adj_list:
            return self.adj_list[vertex]
        return []
    
    def print_graph(self):
        """
        Выводит граф в виде списка смежности.
        """
        for vertex, neighbors in self.adj_list.items():
            neighbors_str = ", ".join([f"{v}({w})" for v, w in neighbors])
            print(f"{vertex}: {neighbors_str}")

# Пример использования
print("\nПример списка смежности:")
graph = Graph()
graph.add_edge("A", "B")
graph.add_edge("A", "E")
graph.add_edge("B", "C")
graph.add_edge("B", "D")
graph.add_edge("B", "E")
graph.add_edge("C", "D")
graph.add_edge("D", "E")

graph.print_graph()
print(f"Соседи вершины B: {graph.get_neighbors('B')}")
```

**Преимущества списка смежности:**
- Эффективное использование памяти для разреженных графов
- Быстрая итерация по соседям вершины
- Эффективность при добавлении/удалении вершин

**Недостатки списка смежности:**
- Более медленная проверка наличия ребра между двумя вершинами (O(V) в худшем случае)
- Сложность реализации для некоторых алгоритмов

### 3. Матрица инцидентности (Incidence Matrix)

Матрица инцидентности — это матрица размера |V|×|E|, где |V| — количество вершин, а |E| — количество ребер. Если вершина v инцидентна ребру e, то элемент matrix[v][e] равен 1 (или -1 для начальной вершины и 1 для конечной в ориентированном графе).

```python
class IncidenceGraph:
    """
    Представление графа с помощью матрицы инцидентности.
    """
    def __init__(self, num_vertices, directed=False):
        """
        Инициализирует граф с заданным количеством вершин.
        
        Args:
            num_vertices: Количество вершин в графе
            directed: Флаг, указывающий, ориентированный ли граф
        """
        self.num_vertices = num_vertices
        self.directed = directed
        self.num_edges = 0
        self.matrix = []  # Матрица инцидентности будет расти с добавлением ребер
        self.edge_map = {}  # Отображение (v1, v2) -> индекс ребра
    
    def add_edge(self, v1, v2, weight=1):
        """
        Добавляет ребро между вершинами v1 и v2.
        
        Args:
            v1: Начальная вершина
            v2: Конечная вершина
            weight: Вес ребра (по умолчанию 1)
        """
        if v1 >= self.num_vertices or v2 >= self.num_vertices or v1 < 0 or v2 < 0:
            raise ValueError("Вершины должны быть в диапазоне 0...num_vertices-1")
        
        # Проверяем, существует ли уже такое ребро
        if (v1, v2) in self.edge_map or (v2, v1) in self.edge_map and not self.directed:
            return
        
        # Создаем новый столбец для нового ребра
        edge_col = [0] * self.num_vertices
        
        if self.directed:
            edge_col[v1] = -1  # Исходящее ребро
            edge_col[v2] = 1   # Входящее ребро
        else:
            edge_col[v1] = 1
            edge_col[v2] = 1
        
        # Добавляем столбец в матрицу
        self.matrix.append(edge_col)
        
        # Обновляем отображение ребер
        self.edge_map[(v1, v2)] = self.num_edges
        if not self.directed:
            self.edge_map[(v2, v1)] = self.num_edges
        
        self.num_edges += 1
    
    def print_graph(self):
        """
        Выводит граф в виде матрицы инцидентности.
        """
        if not self.matrix:
            print("Пустой граф")
            return
        
        # Транспонируем матрицу для удобства вывода
        transposed = list(zip(*self.matrix))
        
        for i, row in enumerate(transposed):
            print(f"Вершина {i}: {row}")

# Пример использования
print("\nПример матрицы инцидентности:")
graph = IncidenceGraph(5)
graph.add_edge(0, 1)
graph.add_edge(0, 4)
graph.add_edge(1, 2)
graph.add_edge(1, 3)
graph.add_edge(1, 4)
graph.add_edge(2, 3)
graph.add_edge(3, 4)

graph.print_graph()
```

**Преимущества матрицы инцидентности:**
- Простота представления кратных ребер и петель
- Удобство для некоторых специфических алгоритмов

**Недостатки матрицы инцидентности:**
- Высокая пространственная сложность для графов с большим количеством ребер
- Неэффективность для многих стандартных алгоритмов

## Обход графа

Обход графа — это процесс посещения всех вершин графа в определенном порядке. Существует два основных алгоритма обхода: обход в глубину (DFS) и обход в ширину (BFS).

### 1. Обход в глубину (Depth-First Search, DFS)

Алгоритм DFS начинает с некоторой вершины и исследует настолько глубоко, насколько возможно, вдоль каждого пути, прежде чем вернуться назад (backtracking).

```python
def dfs_recursive(graph, vertex, visited=None):
    """
    Рекурсивная реализация обхода в глубину.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        vertex: Текущая вершина
        visited: Множество посещенных вершин
        
    Returns:
        Список вершин в порядке их посещения
    """
    if visited is None:
        visited = set()
    
    if vertex not in visited:
        print(f"Посещена вершина: {vertex}")
        visited.add(vertex)
        
        for neighbor, _ in graph.get_neighbors(vertex):
            dfs_recursive(graph, neighbor, visited)
    
    return visited

def dfs_iterative(graph, start_vertex):
    """
    Итеративная реализация обхода в глубину.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        start_vertex: Начальная вершина
        
    Returns:
        Список вершин в порядке их посещения
    """
    visited = set()
    stack = [start_vertex]
    
    while stack:
        vertex = stack.pop()
        
        if vertex not in visited:
            print(f"Посещена вершина: {vertex}")
            visited.add(vertex)
            
            # Добавляем соседей в обратном порядке, чтобы они обрабатывались в правильном порядке
            neighbors = [v for v, _ in graph.get_neighbors(vertex)]
            for neighbor in reversed(neighbors):
                if neighbor not in visited:
                    stack.append(neighbor)
    
    return visited

# Пример использования
print("\nОбход в глубину (DFS):")
graph = Graph()
graph.add_edge("A", "B")
graph.add_edge("A", "C")
graph.add_edge("B", "D")
graph.add_edge("B", "E")
graph.add_edge("C", "F")
graph.add_edge("E", "F")
graph.add_edge("E", "G")
graph.add_edge("F", "G")

print("Рекурсивный DFS:")
dfs_recursive(graph, "A")

print("\nИтеративный DFS:")
dfs_iterative(graph, "A")
```

### 2. Обход в ширину (Breadth-First Search, BFS)

Алгоритм BFS начинает с некоторой вершины и сначала исследует всех ее соседей, затем соседей соседей и так далее.

```python
def bfs(graph, start_vertex):
    """
    Реализация обхода в ширину.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        start_vertex: Начальная вершина
        
    Returns:
        Список вершин в порядке их посещения
    """
    visited = set()
    queue = [start_vertex]
    visited.add(start_vertex)
    
    while queue:
        vertex = queue.pop(0)
        print(f"Посещена вершина: {vertex}")
        
        for neighbor, _ in graph.get_neighbors(vertex):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return visited

# Пример использования
print("\nОбход в ширину (BFS):")
bfs(graph, "A")
```

### 3. Применение DFS и BFS

#### Поиск пути между двумя вершинами

```python
def find_path_dfs(graph, start, end, visited=None, path=None):
    """
    Поиск пути от start до end с помощью DFS.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        start: Начальная вершина
        end: Конечная вершина
        visited: Множество посещенных вершин
        path: Текущий путь
        
    Returns:
        Путь от start до end или None, если путь не найден
    """
    if visited is None:
        visited = set()
    if path is None:
        path = []
    
    visited.add(start)
    path.append(start)
    
    if start == end:
        return path
    
    for neighbor, _ in graph.get_neighbors(start):
        if neighbor not in visited:
            result = find_path_dfs(graph, neighbor, end, visited, path)
            if result:
                return result
    
    # Если путь не найден, возвращаемся назад
    path.pop()
    return None

def find_path_bfs(graph, start, end):
    """
    Поиск кратчайшего пути от start до end с помощью BFS.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        start: Начальная вершина
        end: Конечная вершина
        
    Returns:
        Кратчайший путь от start до end или None, если путь не найден
    """
    visited = {start: None}  # Для каждой вершины храним ее предшественника
    queue = [start]
    
    while queue:
        vertex = queue.pop(0)
        
        if vertex == end:
            # Восстанавливаем путь
            path = []
            while vertex is not None:
                path.append(vertex)
                vertex = visited[vertex]
            return list(reversed(path))
        
        for neighbor, _ in graph.get_neighbors(vertex):
            if neighbor not in visited:
                visited[neighbor] = vertex
                queue.append(neighbor)
    
    return None

# Пример использования
print("\nПоиск пути:")
path_dfs = find_path_dfs(graph, "A", "G")
print(f"Путь DFS от A до G: {path_dfs}")

path_bfs = find_path_bfs(graph, "A", "G")
print(f"Путь BFS от A до G: {path_bfs}")
```

#### Проверка на цикличность графа

```python
def has_cycle_dfs(graph, vertex, visited=None, parent=None):
    """
    Проверяет, содержит ли неориентированный граф цикл.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        vertex: Текущая вершина
        visited: Множество посещенных вершин
        parent: Родительская вершина
        
    Returns:
        True, если граф содержит цикл, иначе False
    """
    if visited is None:
        visited = set()
    
    visited.add(vertex)
    
    for neighbor, _ in graph.get_neighbors(vertex):
        # Если сосед не посещен, рекурсивно проверяем его
        if neighbor not in visited:
            if has_cycle_dfs(graph, neighbor, visited, vertex):
                return True
        # Если сосед посещен и не является родителем, найден цикл
        elif parent != neighbor:
            return True
    
    return False

def has_cycle_in_directed_graph(graph):
    """
    Проверяет, содержит ли ориентированный граф цикл.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        
    Returns:
        True, если граф содержит цикл, иначе False
    """
    visited = set()  # Вершины, посещенные в текущем обходе
    rec_stack = set()  # Вершины в текущем рекурсивном стеке
    
    def dfs(vertex):
        visited.add(vertex)
        rec_stack.add(vertex)
        
        for neighbor, _ in graph.get_neighbors(vertex):
            if neighbor not in visited:
                if dfs(neighbor):
                    return True
            elif neighbor in rec_stack:
                return True
        
        rec_stack.remove(vertex)
        return False
    
    # Проверяем каждую вершину как начальную для DFS
    for vertex in graph.vertices:
        if vertex not in visited:
            if dfs(vertex):
                return True
    
    return False

# Пример использования
print("\nПроверка на цикличность:")
cycle_graph = Graph()
cycle_graph.add_edge("A", "B")
cycle_graph.add_edge("B", "C")
cycle_graph.add_edge("C", "A")

print(f"Граф содержит цикл: {has_cycle_dfs(cycle_graph, 'A')}")

directed_graph = Graph(directed=True)
directed_graph.add_edge("A", "B")
directed_graph.add_edge("B", "C")
directed_graph.add_edge("C", "A")

print(f"Ориентированный граф содержит цикл: {has_cycle_in_directed_graph(directed_graph)}")
```

#### Компоненты связности

```python
def find_connected_components(graph):
    """
    Находит компоненты связности в неориентированном графе.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        
    Returns:
        Список компонент связности, где каждая компонента - множество вершин
    """
    visited = set()
    components = []
    
    for vertex in graph.vertices:
        if vertex not in visited:
            component = set()
            dfs_component(graph, vertex, visited, component)
            components.append(component)
    
    return components

def dfs_component(graph, vertex, visited, component):
    """
    Вспомогательная функция для поиска компонент связности.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        vertex: Текущая вершина
        visited: Множество посещенных вершин
        component: Текущая компонента связности
    """
    visited.add(vertex)
    component.add(vertex)
    
    for neighbor, _ in graph.get_neighbors(vertex):
        if neighbor not in visited:
            dfs_component(graph, neighbor, visited, component)

# Пример использования
print("\nКомпоненты связности:")
disconnected_graph = Graph()
disconnected_graph.add_edge("A", "B")
disconnected_graph.add_edge("A", "C")
disconnected_graph.add_edge("D", "E")
disconnected_graph.add_edge("F", "G")
disconnected_graph.add_edge("F", "H")

components = find_connected_components(disconnected_graph)
print(f"Найдено {len(components)} компонент связности:")
for i, component in enumerate(components):
    print(f"Компонента {i+1}: {component}")
```

## Алгоритмы поиска кратчайшего пути

### 1. Алгоритм Дейкстры (Dijkstra's Algorithm)

Алгоритм Дейкстры находит кратчайшие пути от одной вершины до всех остальных в графе с неотрицательными весами ребер.

```python
import heapq

def dijkstra(graph, start_vertex):
    """
    Реализация алгоритма Дейкстры для поиска кратчайших путей.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        start_vertex: Начальная вершина
        
    Returns:
        Два словаря: distances и predecessors
        distances[v] - расстояние от start_vertex до v
        predecessors[v] - предшественник v в кратчайшем пути
    """
    # Инициализируем расстояния и предшественников
    distances = {vertex: float('infinity') for vertex in graph.vertices}
    distances[start_vertex] = 0
    predecessors = {vertex: None for vertex in graph.vertices}
    
    # Очередь с приоритетом для вершин, (расстояние, вершина)
    priority_queue = [(0, start_vertex)]
    
    while priority_queue:
        # Извлекаем вершину с наименьшим расстоянием
        current_distance, current_vertex = heapq.heappop(priority_queue)
        
        # Если мы уже нашли кратчайший путь до этой вершины, пропускаем
        if current_distance > distances[current_vertex]:
            continue
        
        # Просматриваем всех соседей текущей вершины
        for neighbor, weight in graph.get_neighbors(current_vertex):
            distance = current_distance + weight
            
            # Если найден более короткий путь, обновляем расстояние и предшественника
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                predecessors[neighbor] = current_vertex
                heapq.heappush(priority_queue, (distance, neighbor))
    
    return distances, predecessors

def reconstruct_path(predecessors, end_vertex):
    """
    Восстанавливает путь от начальной вершины до end_vertex.
    
    Args:
        predecessors: Словарь предшественников
        end_vertex: Конечная вершина
        
    Returns:
        Список вершин, составляющих путь
    """
    path = []
    current = end_vertex
    
    while current is not None:
        path.append(current)
        current = predecessors[current]
    
    return list(reversed(path))

# Пример использования
print("\nАлгоритм Дейкстры:")
weighted_graph = Graph(directed=True)
weighted_graph.add_edge("A", "B", 4)
weighted_graph.add_edge("A", "C", 2)
weighted_graph.add_edge("B", "C", 5)
weighted_graph.add_edge("B", "D", 10)
weighted_graph.add_edge("C", "D", 3)
weighted_graph.add_edge("C", "E", 6)
weighted_graph.add_edge("D", "E", 2)

distances, predecessors = dijkstra(weighted_graph, "A")
print("Расстояния от A до всех вершин:")
for vertex, distance in distances.items():
    print(f"A -> {vertex}: {distance}")

print("\nКратчайшие пути от A:")
for vertex in weighted_graph.vertices:
    if vertex != "A":
        path = reconstruct_path(predecessors, vertex)
        print(f"A -> {vertex}: {' -> '.join(path)}")
```

### 2. Алгоритм Беллмана-Форда (Bellman-Ford Algorithm)

Алгоритм Беллмана-Форда находит кратчайшие пути от одной вершины до всех остальных в графе, который может содержать ребра с отрицательными весами, и может определить наличие цикла отрицательного веса.

```python
def bellman_ford(graph, start_vertex):
    """
    Реализация алгоритма Беллмана-Форда для поиска кратчайших путей.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        start_vertex: Начальная вершина
        
    Returns:
        Три значения: distances, predecessors, negative_cycle
        distances[v] - расстояние от start_vertex до v
        predecessors[v] - предшественник v в кратчайшем пути
        negative_cycle - True, если в графе есть цикл отрицательного веса
    """
    # Инициализируем расстояния и предшественников
    distances = {vertex: float('infinity') for vertex in graph.vertices}
    distances[start_vertex] = 0
    predecessors = {vertex: None for vertex in graph.vertices}
    
    # Собираем все ребра графа
    edges = []
    for vertex in graph.vertices:
        for neighbor, weight in graph.get_neighbors(vertex):
            edges.append((vertex, neighbor, weight))
    
    # Релаксация рёбер |V| - 1 раз
    for _ in range(len(graph.vertices) - 1):
        for u, v, weight in edges:
            if distances[u] != float('infinity') and distances[u] + weight < distances[v]:
                distances[v] = distances[u] + weight
                predecessors[v] = u
    
    # Проверка на наличие цикла отрицательного веса
    negative_cycle = False
    for u, v, weight in edges:
        if distances[u] != float('infinity') and distances[u] + weight < distances[v]:
            negative_cycle = True
            break
    
    return distances, predecessors, negative_cycle

# Пример использования
print("\nАлгоритм Беллмана-Форда:")
negative_weight_graph = Graph(directed=True)
negative_weight_graph.add_edge("A", "B", 4)
negative_weight_graph.add_edge("A", "C", 2)
negative_weight_graph.add_edge("B", "C", -5)
negative_weight_graph.add_edge("B", "D", 10)
negative_weight_graph.add_edge("C", "D", 3)
negative_weight_graph.add_edge("C", "E", 6)
negative_weight_graph.add_edge("D", "E", 2)

distances, predecessors, negative_cycle = bellman_ford(negative_weight_graph, "A")

if negative_cycle:
    print("Граф содержит цикл отрицательного веса")
else:
    print("Расстояния от A до всех вершин:")
    for vertex, distance in distances.items():
        print(f"A -> {vertex}: {distance}")

    print("\nКратчайшие пути от A:")
    for vertex in negative_weight_graph.vertices:
        if vertex != "A":
            path = reconstruct_path(predecessors, vertex)
            print(f"A -> {vertex}: {' -> '.join(path)}")

# Граф с циклом отрицательного веса
print("\nГраф с циклом отрицательного веса:")
negative_cycle_graph = Graph(directed=True)
negative_cycle_graph.add_edge("A", "B", 1)
negative_cycle_graph.add_edge("B", "C", 2)
negative_cycle_graph.add_edge("C", "D", 3)
negative_cycle_graph.add_edge("D", "B", -7)

distances, predecessors, negative_cycle = bellman_ford(negative_cycle_graph, "A")

if negative_cycle:
    print("Граф содержит цикл отрицательного веса")
else:
    print("Граф не содержит цикл отрицательного веса")
```

### 3. Алгоритм Флойда-Уоршелла (Floyd-Warshall Algorithm)

Алгоритм Флойда-Уоршелла находит кратчайшие пути между всеми парами вершин в графе.

```python
def floyd_warshall(graph):
    """
    Реализация алгоритма Флойда-Уоршелла для поиска кратчайших путей между всеми парами вершин.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        
    Returns:
        Два словаря: distances и next_vertices
        distances[u][v] - расстояние от u до v
        next_vertices[u][v] - следующая вершина на пути от u до v
    """
    # Строим отображение имен вершин в индексы
    vertices = list(graph.vertices)
    n = len(vertices)
    vertex_to_index = {vertices[i]: i for i in range(n)}
    
    # Инициализируем матрицу расстояний и матрицу следующих вершин
    distances = [[float('infinity')] * n for _ in range(n)]
    next_vertices = [[None] * n for _ in range(n)]
    
    # Заполняем матрицы начальными значениями
    for i in range(n):
        distances[i][i] = 0
    
    for u in vertices:
        u_idx = vertex_to_index[u]
        for v, weight in graph.get_neighbors(u):
            v_idx = vertex_to_index[v]
            distances[u_idx][v_idx] = weight
            next_vertices[u_idx][v_idx] = v
    
    # Алгоритм Флойда-Уоршелла
    for k in range(n):
        for i in range(n):
            for j in range(n):
                if distances[i][k] != float('infinity') and distances[k][j] != float('infinity'):
                    if distances[i][j] > distances[i][k] + distances[k][j]:
                        distances[i][j] = distances[i][k] + distances[k][j]
                        next_vertices[i][j] = next_vertices[i][k]
    
    # Преобразуем матрицы обратно в словари
    distance_dict = {}
    next_vertex_dict = {}
    
    for u in vertices:
        distance_dict[u] = {}
        next_vertex_dict[u] = {}
        u_idx = vertex_to_index[u]
        
        for v in vertices:
            v_idx = vertex_to_index[v]
            distance_dict[u][v] = distances[u_idx][v_idx]
            next_vertex_dict[u][v] = next_vertices[u_idx][v_idx]
    
    return distance_dict, next_vertex_dict

def construct_path_floyd_warshall(next_vertices, u, v):
    """
    Восстанавливает путь от u до v по матрице следующих вершин.
    
    Args:
        next_vertices: Матрица следующих вершин
        u: Начальная вершина
        v: Конечная вершина
        
    Returns:
        Список вершин, составляющих путь, или None, если путь не существует
    """
    if next_vertices[u][v] is None:
        return None
    
    path = [u]
    while u != v:
        u = next_vertices[u][v]
        path.append(u)
    
    return path

# Пример использования
print("\nАлгоритм Флойда-Уоршелла:")
floyd_graph = Graph(directed=True)
floyd_graph.add_edge("A", "B", 3)
floyd_graph.add_edge("A", "C", 6)
floyd_graph.add_edge("B", "C", 2)
floyd_graph.add_edge("B", "D", 1)
floyd_graph.add_edge("C", "D", 4)
floyd_graph.add_edge("D", "A", 5)

distances, next_vertices = floyd_warshall(floyd_graph)

print("Матрица расстояний между всеми парами вершин:")
for u in floyd_graph.vertices:
    for v in floyd_graph.vertices:
        if distances[u][v] == float('infinity'):
            print(f"{u} -> {v}: INF")
        else:
            print(f"{u} -> {v}: {distances[u][v]}")

print("\nКратчайшие пути между всеми парами вершин:")
for u in floyd_graph.vertices:
    for v in floyd_graph.vertices:
        if u != v:
            path = construct_path_floyd_warshall(next_vertices, u, v)
            if path:
                print(f"{u} -> {v}: {' -> '.join(path)}")
            else:
                print(f"{u} -> {v}: Путь не существует")
```

## Алгоритмы построения минимального остовного дерева

### 1. Алгоритм Прима (Prim's Algorithm)

Алгоритм Прима находит минимальное остовное дерево для взвешенного неориентированного графа.

```python
def prim(graph):
    """
    Реализация алгоритма Прима для нахождения минимального остовного дерева.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        
    Returns:
        Список ребер минимального остовного дерева (MST)
    """
    if not graph.vertices:
        return []
    
    # Инициализируем множество вершин в MST и список ребер
    mst_vertices = set()
    mst_edges = []
    
    # Начинаем с произвольной вершины
    start_vertex = next(iter(graph.vertices))
    mst_vertices.add(start_vertex)
    
    # Приоритетная очередь для ребер, (вес, (u, v))
    edges_priority_queue = []
    
    # Добавляем все ребра, исходящие из начальной вершины
    for neighbor, weight in graph.get_neighbors(start_vertex):
        heapq.heappush(edges_priority_queue, (weight, (start_vertex, neighbor)))
    
    # Продолжаем, пока все вершины не будут в MST
    while len(mst_vertices) < len(graph.vertices) and edges_priority_queue:
        # Извлекаем ребро с наименьшим весом
        weight, (u, v) = heapq.heappop(edges_priority_queue)
        
        # Если добавление этого ребра не создает цикл (одна из вершин не в MST)
        if v not in mst_vertices:
            # Добавляем вершину и ребро в MST
            mst_vertices.add(v)
            mst_edges.append((u, v, weight))
            
            # Добавляем все ребра, исходящие из новой вершины
            for neighbor, edge_weight in graph.get_neighbors(v):
                if neighbor not in mst_vertices:
                    heapq.heappush(edges_priority_queue, (edge_weight, (v, neighbor)))
    
    return mst_edges

# Пример использования
print("\nАлгоритм Прима:")
mst_graph = Graph()
mst_graph.add_edge("A", "B", 2)
mst_graph.add_edge("A", "C", 3)
mst_graph.add_edge("B", "C", 1)
mst_graph.add_edge("B", "D", 1)
mst_graph.add_edge("B", "E", 4)
mst_graph.add_edge("C", "D", 3)
mst_graph.add_edge("D", "E", 2)
mst_graph.add_edge("D", "F", 3)
mst_graph.add_edge("E", "F", 5)

mst_edges = prim(mst_graph)
print("Ребра минимального остовного дерева:")
for u, v, weight in mst_edges:
    print(f"{u} -- {v}: {weight}")
print(f"Общий вес MST: {sum(weight for _, _, weight in mst_edges)}")
```

### 2. Алгоритм Краскала (Kruskal's Algorithm)

Алгоритм Краскала также находит минимальное остовное дерево, но использует другой подход.

```python
class DisjointSet:
    """
    Реализация системы непересекающихся множеств (Union-Find).
    """
    def __init__(self, vertices):
        """
        Инициализирует систему непересекающихся множеств.
        
        Args:
            vertices: Список вершин
        """
        self.parent = {vertex: vertex for vertex in vertices}
        self.rank = {vertex: 0 for vertex in vertices}
    
    def find(self, vertex):
        """
        Находит корень (представителя) множества, содержащего vertex.
        
        Args:
            vertex: Вершина
            
        Returns:
            Корень множества
        """
        if self.parent[vertex] != vertex:
            self.parent[vertex] = self.find(self.parent[vertex])  # Сжатие пути
        return self.parent[vertex]
    
    def union(self, vertex1, vertex2):
        """
        Объединяет множества, содержащие vertex1 и vertex2.
        
        Args:
            vertex1: Первая вершина
            vertex2: Вторая вершина
        """
        root1 = self.find(vertex1)
        root2 = self.find(vertex2)
        
        if root1 == root2:
            return
        
        # Объединяем по рангу
        if self.rank[root1] < self.rank[root2]:
            self.parent[root1] = root2
        elif self.rank[root1] > self.rank[root2]:
            self.parent[root2] = root1
        else:
            self.parent[root2] = root1
            self.rank[root1] += 1

def kruskal(graph):
    """
    Реализация алгоритма Краскала для нахождения минимального остовного дерева.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        
    Returns:
        Список ребер минимального остовного дерева (MST)
    """
    # Инициализируем список всех ребер и систему непересекающихся множеств
    edges = []
    for vertex in graph.vertices:
        for neighbor, weight in graph.get_neighbors(vertex):
            # Для неориентированного графа каждое ребро встречается дважды
            # Поэтому добавляем ребро только если vertex < neighbor
            if not graph.directed and vertex < neighbor:
                edges.append((vertex, neighbor, weight))
            elif graph.directed:
                edges.append((vertex, neighbor, weight))
    
    # Сортируем ребра по весу
    edges.sort(key=lambda x: x[2])
    
    # Инициализируем систему непересекающихся множеств
    disjoint_set = DisjointSet(graph.vertices)
    
    # Инициализируем список ребер MST
    mst_edges = []
    
    # Проходим по всем ребрам в порядке возрастания веса
    for u, v, weight in edges:
        # Если добавление ребра не создает цикл
        if disjoint_set.find(u) != disjoint_set.find(v):
            # Добавляем ребро в MST
            mst_edges.append((u, v, weight))
            # Объединяем множества
            disjoint_set.union(u, v)
    
    return mst_edges

# Пример использования
print("\nАлгоритм Краскала:")
kruskal_edges = kruskal(mst_graph)
print("Ребра минимального остовного дерева:")
for u, v, weight in kruskal_edges:
    print(f"{u} -- {v}: {weight}")
print(f"Общий вес MST: {sum(weight for _, _, weight in kruskal_edges)}")
```

## Топологическая сортировка

Топологическая сортировка — это линейное упорядочивание вершин ориентированного ациклического графа (DAG) таким образом, что для каждого направленного ребра uv, вершина u идёт перед v в упорядочивании.

```python
def topological_sort_dfs(graph):
    """
    Топологическая сортировка ориентированного ациклического графа (DAG) с помощью DFS.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        
    Returns:
        Список вершин в топологическом порядке или None, если граф содержит цикл
    """
    # Проверяем, что граф ориентированный
    if not graph.directed:
        raise ValueError("Граф должен быть ориентированным")
    
    # Проверяем наличие циклов
    if has_cycle_in_directed_graph(graph):
        return None
    
    visited = set()
    topo_order = []
    
    def dfs(vertex):
        visited.add(vertex)
        for neighbor, _ in graph.get_neighbors(vertex):
            if neighbor not in visited:
                dfs(neighbor)
        topo_order.append(vertex)
    
    # Для каждой вершины, не посещенной ранее, запускаем DFS
    for vertex in graph.vertices:
        if vertex not in visited:
            dfs(vertex)
    
    # Переворачиваем список для получения правильного топологического порядка
    return list(reversed(topo_order))

def topological_sort_kahn(graph):
    """
    Топологическая сортировка ориентированного ациклического графа (DAG) с помощью алгоритма Кана.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        
    Returns:
        Список вершин в топологическом порядке или None, если граф содержит цикл
    """
    # Проверяем, что граф ориентированный
    if not graph.directed:
        raise ValueError("Граф должен быть ориентированным")
    
    # Подсчитываем входящие степени для всех вершин
    in_degree = {vertex: 0 for vertex in graph.vertices}
    for vertex in graph.vertices:
        for neighbor, _ in graph.get_neighbors(vertex):
            in_degree[neighbor] += 1
    
    # Помещаем в очередь все вершины с нулевой входящей степенью
    queue = [vertex for vertex, degree in in_degree.items() if degree == 0]
    topo_order = []
    
    while queue:
        # Извлекаем вершину из очереди и добавляем в результат
        vertex = queue.pop(0)
        topo_order.append(vertex)
        
        # Для каждого соседа уменьшаем входящую степень
        for neighbor, _ in graph.get_neighbors(vertex):
            in_degree[neighbor] -= 1
            # Если входящая степень стала нулевой, добавляем в очередь
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    # Если размер топологического порядка не равен количеству вершин,
    # значит, в графе есть цикл
    if len(topo_order) != len(graph.vertices):
        return None
    
    return topo_order

# Пример использования
print("\nТопологическая сортировка:")
topo_graph = Graph(directed=True)
topo_graph.add_edge("A", "B")
topo_graph.add_edge("A", "C")
topo_graph.add_edge("B", "D")
topo_graph.add_edge("C", "D")
topo_graph.add_edge("D", "E")

topo_order_dfs = topological_sort_dfs(topo_graph)
print(f"Топологический порядок (DFS): {topo_order_dfs}")

topo_order_kahn = topological_sort_kahn(topo_graph)
print(f"Топологический порядок (Kahn): {topo_order_kahn}")

# Граф с циклом
cycle_topo_graph = Graph(directed=True)
cycle_topo_graph.add_edge("A", "B")
cycle_topo_graph.add_edge("B", "C")
cycle_topo_graph.add_edge("C", "A")

topo_order_cycle = topological_sort_kahn(cycle_topo_graph)
print(f"Топологический порядок для графа с циклом: {topo_order_cycle}")
```

## Алгоритмы для сильно связных компонент

### Алгоритм Косарайю (Kosaraju's Algorithm)

Алгоритм Косарайю находит сильно связные компоненты в ориентированном графе.

```python
def kosaraju(graph):
    """
    Реализация алгоритма Косарайю для нахождения сильно связных компонент.
    
    Args:
        graph: Ориентированный граф, представленный в виде списка смежности
        
    Returns:
        Список сильно связных компонент, где каждая компонента — множество вершин
    """
    # Шаг 1: Выполняем DFS и получаем порядок выхода из рекурсии
    stack = []
    visited = set()
    
    def dfs_first_pass(vertex):
        visited.add(vertex)
        for neighbor, _ in graph.get_neighbors(vertex):
            if neighbor not in visited:
                dfs_first_pass(neighbor)
        stack.append(vertex)
    
    for vertex in graph.vertices:
        if vertex not in visited:
            dfs_first_pass(vertex)
    
    # Шаг 2: Строим транспонированный граф (инвертируем ребра)
    transposed_graph = Graph(directed=True)
    for vertex in graph.vertices:
        transposed_graph.add_vertex(vertex)
    
    for vertex in graph.vertices:
        for neighbor, weight in graph.get_neighbors(vertex):
            transposed_graph.add_edge(neighbor, vertex, weight)
    
    # Шаг 3: Выполняем DFS на транспонированном графе в порядке, обратном порядку выхода
    visited = set()
    scc = []
    
    def dfs_second_pass(vertex, component):
        visited.add(vertex)
        component.add(vertex)
        for neighbor, _ in transposed_graph.get_neighbors(vertex):
            if neighbor not in visited:
                dfs_second_pass(neighbor, component)
    
    while stack:
        vertex = stack.pop()
        if vertex not in visited:
            component = set()
            dfs_second_pass(vertex, component)
            scc.append(component)
    
    return scc

# Пример использования
print("\nАлгоритм Косарайю:")
scc_graph = Graph(directed=True)
scc_graph.add_edge("A", "B")
scc_graph.add_edge("B", "C")
scc_graph.add_edge("C", "A")
scc_graph.add_edge("B", "D")
scc_graph.add_edge("D", "E")
scc_graph.add_edge("E", "F")
scc_graph.add_edge("F", "D")

scc = kosaraju(scc_graph)
print(f"Найдено {len(scc)} сильно связных компонент:")
for i, component in enumerate(scc):
    print(f"Компонента {i+1}: {component}")
```

## Практические применения алгоритмов на графах

### 1. Поиск кратчайшего пути в навигационных системах

```python
def navigation_system(graph, start, end):
    """
    Имитирует работу навигационной системы, находя оптимальный маршрут.
    
    Args:
        graph: Взвешенный граф, представляющий карту
        start: Начальная точка
        end: Конечная точка
        
    Returns:
        Кратчайший маршрут и его длину
    """
    distances, predecessors = dijkstra(graph, start)
    
    if distances[end] == float('infinity'):
        return None, float('infinity')
    
    # Восстанавливаем маршрут
    path = reconstruct_path(predecessors, end)
    
    return path, distances[end]

# Пример использования
print("\nПрименение: Навигационная система")
map_graph = Graph()
map_graph.add_edge("Home", "Crossroad1", 5)
map_graph.add_edge("Home", "Crossroad2", 7)
map_graph.add_edge("Crossroad1", "Supermarket", 8)
map_graph.add_edge("Crossroad1", "School", 12)
map_graph.add_edge("Crossroad2", "School", 6)
map_graph.add_edge("Crossroad2", "Park", 9)
map_graph.add_edge("Supermarket", "Park", 10)
map_graph.add_edge("School", "Office", 4)
map_graph.add_edge("Park", "Office", 7)

route, distance = navigation_system(map_graph, "Home", "Office")
print(f"Оптимальный маршрут: {' -> '.join(route)}")
print(f"Расстояние: {distance} км")
```

### 2. Планирование задач с зависимостями

```python
def schedule_tasks(tasks, dependencies):
    """
    Планирует выполнение задач с учетом их зависимостей.
    
    Args:
        tasks: Список задач
        dependencies: Список пар (task1, task2), где task1 должна быть выполнена перед task2
        
    Returns:
        Список задач в порядке их выполнения или None, если есть циклическая зависимость
    """
    # Строим граф зависимостей
    graph = Graph(directed=True)
    for task in tasks:
        graph.add_vertex(task)
    
    for pred, succ in dependencies:
        graph.add_edge(pred, succ)
    
    # Выполняем топологическую сортировку
    schedule = topological_sort_kahn(graph)
    
    return schedule

# Пример использования
print("\nПрименение: Планирование задач")
tasks = ["Install OS", "Setup Dev Environment", "Write Code", "Test", "Deploy", "Document"]
dependencies = [
    ("Install OS", "Setup Dev Environment"),
    ("Setup Dev Environment", "Write Code"),
    ("Write Code", "Test"),
    ("Test", "Deploy"),
    ("Write Code", "Document")
]

schedule = schedule_tasks(tasks, dependencies)
if schedule:
    print("Порядок выполнения задач:")
    for i, task in enumerate(schedule):
        print(f"{i+1}. {task}")
else:
    print("Невозможно составить расписание из-за циклических зависимостей")
```

### 3. Анализ социальных сетей

```python
def analyze_social_network(graph):
    """
    Анализирует социальную сеть, представленную графом.
    
    Args:
        graph: Граф, представляющий социальную сеть
        
    Returns:
        Словарь с результатами анализа
    """
    results = {}
    
    # Вычисляем количество друзей (степень) каждого пользователя
    degree = {}
    for vertex in graph.vertices:
        degree[vertex] = len(graph.get_neighbors(vertex))
    
    # Находим пользователя с наибольшим количеством друзей
    most_friends = max(degree.items(), key=lambda x: x[1])
    results["most_connected_user"] = most_friends[0]
    results["most_friends_count"] = most_friends[1]
    
    # Находим компоненты связности (группы пользователей)
    components = find_connected_components(graph)
    results["communities"] = components
    
    # Находим центральных пользователей (по близости ко всем остальным)
    distances, _ = floyd_warshall(graph)
    
    closeness_centrality = {}
    for vertex in graph.vertices:
        # Сумма расстояний до всех достижимых вершин
        total_distance = sum(dist for v, dist in distances[vertex].items()
                            if dist != float('infinity') and v != vertex)
        # Количество достижимых вершин
        reachable_count = sum(1 for dist in distances[vertex].values()
                             if dist != float('infinity')) - 1
        
        if reachable_count > 0:
            closeness_centrality[vertex] = reachable_count / total_distance
        else:
            closeness_centrality[vertex] = 0
    
    most_central = max(closeness_centrality.items(), key=lambda x: x[1])
    results["most_central_user"] = most_central[0]
    
    return results

# Пример использования
print("\nПрименение: Анализ социальной сети")
social_graph = Graph()
social_graph.add_edge("Alice", "Bob")
social_graph.add_edge("Alice", "Charlie")
social_graph.add_edge("Alice", "David")
social_graph.add_edge("Bob", "Eve")
social_graph.add_edge("Charlie", "David")
social_graph.add_edge("David", "Eve")
social_graph.add_edge("Frank", "George")
social_graph.add_edge("Helen", "Ivan")
social_graph.add_edge("Helen", "Julia")

analysis = analyze_social_network(social_graph)
print(f"Пользователь с наибольшим количеством друзей: {analysis['most_connected_user']} ({analysis['most_friends_count']} друзей)")
print(f"Наиболее центральный пользователь: {analysis['most_central_user']}")
print(f"Количество сообществ: {len(analysis['communities'])}")
for i, community in enumerate(analysis['communities']):
    print(f"Сообщество {i+1}: {community}")
```

### 4. Маршрутизация в компьютерных сетях

```python
def network_routing(network, start_router, end_router):
    """
    Моделирует маршрутизацию пакетов в компьютерной сети.
    
    Args:
        network: Граф, представляющий сеть маршрутизаторов
        start_router: Начальный маршрутизатор
        end_router: Конечный маршрутизатор
        
    Returns:
        Оптимальный маршрут и его задержку
    """
    distances, predecessors = dijkstra(network, start_router)
    
    if distances[end_router] == float('infinity'):
        return None, float('infinity')
    
    # Восстанавливаем маршрут
    path = reconstruct_path(predecessors, end_router)
    
    # Вычисляем задержку на каждом участке маршрута
    delay = 0
    for i in range(len(path) - 1):
        for neighbor, weight in network.get_neighbors(path[i]):
            if neighbor == path[i + 1]:
                delay += weight
                break
    
    return path, delay

# Пример использования
print("\nПрименение: Маршрутизация в компьютерных сетях")
network = Graph(directed=True)
network.add_edge("Router1", "Router2", 5)  # Задержка в мс
network.add_edge("Router1", "Router3", 3)
network.add_edge("Router2", "Router4", 2)
network.add_edge("Router3", "Router4", 6)
network.add_edge("Router3", "Router5", 8)
network.add_edge("Router4", "Router5", 4)
network.add_edge("Router4", "Router6", 7)
network.add_edge("Router5", "Router6", 5)

route, delay = network_routing(network, "Router1", "Router6")
print(f"Оптимальный маршрут: {' -> '.join(route)}")
print(f"Общая задержка: {delay} мс")
```

## Сложные алгоритмы на графах

### 1. Алгоритм поиска шарниров и мостов

Шарнир (точка сочленения) — это вершина, удаление которой увеличивает количество компонент связности графа. Мост — это ребро, удаление которого увеличивает количество компонент связности.

```python
def find_articulation_points_and_bridges(graph):
    """
    Находит шарниры (точки сочленения) и мосты в неориентированном графе.
    
    Args:
        graph: Неориентированный граф, представленный в виде списка смежности
        
    Returns:
        Кортеж (точки_сочленения, мосты)
    """
    if graph.directed:
        raise ValueError("Граф должен быть неориентированным")
    
    # Инициализируем необходимые структуры данных
    discovery_time = {}  # Время обнаружения вершины
    low_value = {}       # Наименьшее время обнаружения, доступное без использования back edge
    parent = {}          # Родитель в DFS-дереве
    articulation_points = set()  # Точки сочленения
    bridges = []               # Мосты
    
    time = 0  # Счетчик времени
    
    def dfs(vertex):
        nonlocal time
        
        # Увеличиваем счетчик времени
        time += 1
        discovery_time[vertex] = time
        low_value[vertex] = time
        children = 0  # Количество дочерних вершин в DFS-дереве
        
        for neighbor, _ in graph.get_neighbors(vertex):
            # Если соседняя вершина еще не посещена
            if neighbor not in discovery_time:
                parent[neighbor] = vertex
                children += 1
                
                dfs(neighbor)
                
                # Обновляем low_value
                low_value[vertex] = min(low_value[vertex], low_value[neighbor])
                
                # Проверяем, является ли вершина точкой сочленения
                # Случай 1: вершина является корнем DFS-дерева и имеет не менее двух дочерних вершин
                if parent.get(vertex) is None and children > 1:
                    articulation_points.add(vertex)
                
                # Случай 2: вершина не является корнем и low_value ее дочерней вершины >= discovery_time вершины
                if parent.get(vertex) is not None and low_value[neighbor] >= discovery_time[vertex]:
                    articulation_points.add(vertex)
                
                # Проверяем, является ли ребро мостом
                if low_value[neighbor] > discovery_time[vertex]:
                    bridges.append((vertex, neighbor))
            
            # Если соседняя вершина уже посещена, но не является родителем
            elif neighbor != parent.get(vertex):
                low_value[vertex] = min(low_value[vertex], discovery_time[neighbor])
    
    # Запускаем DFS из каждой непосещенной вершины
    for vertex in graph.vertices:
        if vertex not in discovery_time:
            dfs(vertex)
    
    return articulation_points, bridges

# Пример использования
print("\nПоиск шарниров и мостов:")
ap_graph = Graph()
ap_graph.add_edge("A", "B")
ap_graph.add_edge("A", "C")
ap_graph.add_edge("B", "C")
ap_graph.add_edge("B", "D")
ap_graph.add_edge("C", "E")
ap_graph.add_edge("D", "E")
ap_graph.add_edge("E", "F")
ap_graph.add_edge("F", "G")
ap_graph.add_edge("G", "H")
ap_graph.add_edge("H", "I")
ap_graph.add_edge("H", "J")
ap_graph.add_edge("I", "J")

articulation_points, bridges = find_articulation_points_and_bridges(ap_graph)
print(f"Шарниры (точки сочленения): {articulation_points}")
print(f"Мосты: {bridges}")
```

### 2. Алгоритм нахождения максимального потока (Ford-Fulkerson)

Алгоритм Форда-Фалкерсона (или Эдмондса-Карпа) находит максимальный поток в сети.

```python
def ford_fulkerson(graph, source, sink):
    """
    Реализация алгоритма Форда-Фалкерсона для нахождения максимального потока.
    
    Args:
        graph: Ориентированный взвешенный граф, представленный в виде списка смежности
        source: Исток
        sink: Сток
        
    Returns:
        Максимальный поток
    """
    if not graph.directed:
        raise ValueError("Граф должен быть ориентированным")
    
    # Создаем остаточную сеть
    residual_graph = Graph(directed=True)
    for vertex in graph.vertices:
        residual_graph.add_vertex(vertex)
    
    for vertex in graph.vertices:
        for neighbor, capacity in graph.get_neighbors(vertex):
            # Добавляем прямое ребро с исходной пропускной способностью
            residual_graph.add_edge(vertex, neighbor, capacity)
            # Добавляем обратное ребро с нулевой пропускной способностью
            if not any(v == vertex for v, _ in residual_graph.get_neighbors(neighbor)):
                residual_graph.add_edge(neighbor, vertex, 0)
    
    max_flow = 0
    
    # Функция для поиска увеличивающего пути с помощью BFS
    def bfs():
        visited = {source: None}
        queue = [source]
        
        while queue:
            vertex = queue.pop(0)
            
            if vertex == sink:
                # Восстанавливаем путь
                path = []
                v = sink
                while v is not None:
                    path.append(v)
                    v = visited[v]
                path.reverse()
                return path
            
            for neighbor, capacity in residual_graph.get_neighbors(vertex):
                if neighbor not in visited and capacity > 0:
                    visited[neighbor] = vertex
                    queue.append(neighbor)
        
        return None
    
    # Основной алгоритм
    while True:
        # Ищем увеличивающий путь
        path = bfs()
        if path is None:
            break
        
        # Находим минимальную пропускную способность на пути
        min_capacity = float('infinity')
        for i in range(len(path) - 1):
            u, v = path[i], path[i + 1]
            for neighbor, capacity in residual_graph.get_neighbors(u):
                if neighbor == v:
                    min_capacity = min(min_capacity, capacity)
                    break
        
        # Обновляем остаточную сеть
        for i in range(len(path) - 1):
            u, v = path[i], path[i + 1]
            
            # Уменьшаем пропускную способность прямого ребра
            found = False
            for j, (neighbor, capacity) in enumerate(residual_graph.adj_list[u]):
                if neighbor == v:
                    residual_graph.adj_list[u][j] = (neighbor, capacity - min_capacity)
                    found = True
                    break
            
            # Увеличиваем пропускную способность обратного ребра
            found = False
            for j, (neighbor, capacity) in enumerate(residual_graph.adj_list[v]):
                if neighbor == u:
                    residual_graph.adj_list[v][j] = (neighbor, capacity + min_capacity)
                    found = True
                    break
        
        # Увеличиваем максимальный поток
        max_flow += min_capacity
    
    return max_flow

# Пример использования
print("\nАлгоритм Форда-Фалкерсона:")
flow_graph = Graph(directed=True)
flow_graph.add_edge("S", "A", 10)
flow_graph.add_edge("S", "B", 8)
flow_graph.add_edge("A", "B", 2)
flow_graph.add_edge("A", "C", 5)
flow_graph.add_edge("B", "C", 7)
flow_graph.add_edge("B", "D", 10)
flow_graph.add_edge("C", "D", 8)
flow_graph.add_edge("C", "T", 7)
flow_graph.add_edge("D", "T", 10)

max_flow = ford_fulkerson(flow_graph, "S", "T")
print(f"Максимальный поток: {max_flow}")
```

### 3. Нахождение эйлерова пути/цикла

Эйлеров путь — это путь в графе, проходящий через каждое ребро ровно один раз. Эйлеров цикл — это эйлеров путь, который начинается и заканчивается в одной и той же вершине.

```python
def find_eulerian_path(graph):
    """
    Находит эйлеров путь или цикл в графе.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        
    Returns:
        Список вершин, составляющих эйлеров путь/цикл, или None, если путь не существует
    """
    # Проверяем, существует ли эйлеров путь или цикл
    odd_degree_vertices = []
    for vertex in graph.vertices:
        if len(graph.get_neighbors(vertex)) % 2 != 0:
            odd_degree_vertices.append(vertex)
    
    # Если более двух вершин нечетной степени, эйлеров путь не существует
    if len(odd_degree_vertices) > 2:
        return None
    
    # Выбираем начальную вершину
    start_vertex = None
    if len(odd_degree_vertices) == 0:
        # Если все вершины четной степени, можно начать с любой вершины
        start_vertex = next(iter(graph.vertices))
    else:
        # Если есть вершины нечетной степени, начинаем с одной из них
        start_vertex = odd_degree_vertices[0]
    
    # Создаем копию графа для модификации
    temp_graph = Graph(graph.directed)
    for vertex in graph.vertices:
        temp_graph.add_vertex(vertex)
    
    for vertex in graph.vertices:
        for neighbor, weight in graph.get_neighbors(vertex):
            temp_graph.add_edge(vertex, neighbor, weight)
    
    # Алгоритм Флери для нахождения эйлерова пути
    path = []
    
    def dfs(vertex):
        while temp_graph.get_neighbors(vertex):
            # Выбираем следующее ребро (не мост, если возможно)
            next_vertex = None
            for neighbor, _ in temp_graph.get_neighbors(vertex):
                # Проверяем, является ли ребро мостом
                temp_graph.remove_edge(vertex, neighbor)
                is_bridge = not is_connected(temp_graph, vertex, neighbor)
                temp_graph.add_edge(vertex, neighbor)
                
                if not is_bridge or len(temp_graph.get_neighbors(vertex)) == 1:
                    next_vertex = neighbor
                    break
            
            # Удаляем ребро и продолжаем
            temp_graph.remove_edge(vertex, next_vertex)
            if not graph.directed:
                temp_graph.remove_edge(next_vertex, vertex)
            
            dfs(next_vertex)
        
        path.append(vertex)
    
    dfs(start_vertex)
    path.reverse()
    
    return path

def is_connected(graph, u, v):
    """
    Проверяет, существует ли путь между вершинами u и v.
    
    Args:
        graph: Граф, представленный в виде списка смежности
        u: Первая вершина
        v: Вторая вершина
        
    Returns:
        True, если путь существует, иначе False
    """
    visited = set()
    
    def dfs(vertex):
        visited.add(vertex)
        if vertex == v:
            return True
        
        for neighbor, _ in graph.get_neighbors(vertex):
            if neighbor not in visited:
                if dfs(neighbor):
                    return True
        
        return False
    
    return dfs(u)

# Пример использования
print("\nПоиск эйлерова пути/цикла:")
euler_graph = Graph()
euler_graph.add_edge("A", "B")
euler_graph.add_edge("A", "C")
euler_graph.add_edge("B", "C")
euler_graph.add_edge("B", "D")
euler_graph.add_edge("C", "D")
euler_graph.add_edge("C", "E")
euler_graph.add_edge("D", "E")

euler_path = find_eulerian_path(euler_graph)
if euler_path:
    print(f"Эйлеров путь: {' -> '.join(euler_path)}")
else:
    print("Эйлеров путь не существует")

# Граф с эйлеровым циклом
euler_cycle_graph = Graph()
euler_cycle_graph.add_edge("A", "B")
euler_cycle_graph.add_edge("B", "C")
euler_cycle_graph.add_edge("C", "D")
euler_cycle_graph.add_edge("D", "E")
euler_cycle_graph.add_edge("E", "B")
euler_cycle_graph.add_edge("B", "F")
euler_cycle_graph.add_edge("F", "A")

euler_cycle = find_eulerian_path(euler_cycle_graph)
if euler_cycle:
    print(f"Эйлеров цикл: {' -> '.join(euler_cycle)}")
else:
    print("Эйлеров цикл не существует")
```

## Преимущества и недостатки различных представлений графов

### Матрица смежности

**Преимущества:**
- Простота реализации и понимания
- Быстрый доступ к ребрам (O(1))
- Эффективная проверка наличия ребра
- Подходит для плотных графов

**Недостатки:**
- Высокие затраты памяти (O(V²))
- Неэффективна для разреженных графов
- Медленное добавление/удаление вершин
- Медленное перечисление соседей вершины в разреженных графах

### Список смежности

**Преимущества:**
- Экономия памяти для разреженных графов (O(V+E))
- Быстрое перечисление соседей вершины
- Эффективное добавление/удаление вершин
- Подходит для большинства алгоритмов на графах

**Недостатки:**
- Медленный доступ к ребрам (O(V) в худшем случае)
- Более сложная реализация некоторых алгоритмов
- Неэффективен для очень плотных графов

### Матрица инцидентности

**Преимущества:**
- Удобно представляет мультиграфы
- Хорошо подходит для некоторых специфических алгоритмов
- Явно представляет ребра как сущности

**Недостатки:**
- Высокие затраты памяти для графов с большим количеством ребер (O(V×E))
- Неэффективна для большинства стандартных алгоритмов
- Сложная модификация при добавлении/удалении вершин или ребер

## Заключение

Графы и алгоритмы на графах являются мощным инструментом для моделирования и решения широкого спектра задач. От поиска кратчайшего пути и минимального остовного дерева до анализа сетей и планирования задач — графы находят применение во многих областях информатики и реальной жизни.

В этом разделе мы рассмотрели основные типы графов, способы их представления, ключевые алгоритмы для обхода графов, поиска кратчайших путей, нахождения минимальных остовных деревьев, топологической сортировки и сильно связных компонент. Мы также изучили некоторые сложные алгоритмы, такие как нахождение шарниров и мостов, максимального потока и эйлерова пути.

Важно выбирать подходящее представление графа и алгоритм в зависимости от конкретной задачи и характеристик графа. Правильный выбор может значительно повлиять на эффективность и производительность решения.

Понимание алгоритмов на графах является фундаментальным навыком для любого программиста, позволяющим решать сложные задачи оптимизации, маршрутизации, планирования и анализа данных.

---

В следующем разделе мы рассмотрим строковые алгоритмы, включая алгоритмы поиска подстроки, регулярные выражения и вычисление расстояния Левенштейна, которые находят широкое применение в обработке текста и биоинформатике.
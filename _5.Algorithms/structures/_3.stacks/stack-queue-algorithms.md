# Алгоритмы на стеках и очередях

Стеки и очереди — это фундаментальные структуры данных, которые находят широкое применение в программировании. Они предоставляют разные способы организации и доступа к данным, что делает их незаменимыми инструментами для решения различных задач. В этом разделе мы рассмотрим эти структуры данных и алгоритмы, построенные на их основе.

## Стек (Stack)

**Стек** — это структура данных, работающая по принципу LIFO (Last In, First Out): последний добавленный элемент извлекается первым. Стек можно представить как стопку тарелок — вы можете добавить тарелку сверху или снять верхнюю тарелку, но не можете добраться до тарелок в середине или внизу стопки без удаления верхних.

### Основные операции стека:

1. **push** — добавление элемента на вершину стека
2. **pop** — удаление и возврат элемента с вершины стека
3. **peek** (или **top**) — просмотр элемента на вершине стека без его удаления
4. **isEmpty** — проверка, пуст ли стек

### Реализация стека в Python

```python
class Stack:
    """
    Реализация стека с использованием списка Python.
    """
    def __init__(self):
        self.items = []
    
    def push(self, item):
        """Добавляет элемент на вершину стека."""
        self.items.append(item)
    
    def pop(self):
        """
        Удаляет и возвращает элемент с вершины стека.
        Вызывает исключение, если стек пуст.
        """
        if self.is_empty():
            raise Exception("Ошибка: стек пуст")
        return self.items.pop()
    
    def peek(self):
        """
        Возвращает элемент с вершины стека без его удаления.
        Вызывает исключение, если стек пуст.
        """
        if self.is_empty():
            raise Exception("Ошибка: стек пуст")
        return self.items[-1]
    
    def is_empty(self):
        """Проверяет, пуст ли стек."""
        return len(self.items) == 0
    
    def size(self):
        """Возвращает количество элементов в стеке."""
        return len(self.items)
    
    def __str__(self):
        """Возвращает строковое представление стека."""
        return str(self.items)

# Пример использования
stack = Stack()
stack.push(1)
stack.push(2)
stack.push(3)

print(f"Стек: {stack}")
print(f"Вершина стека: {stack.peek()}")
print(f"Извлекаем элемент: {stack.pop()}")
print(f"Стек после извлечения: {stack}")
```

## Очередь (Queue)

**Очередь** — это структура данных, работающая по принципу FIFO (First In, First Out): первый добавленный элемент извлекается первым. Очередь можно представить как обычную очередь людей — новые люди встают в конец очереди, а обслуживается человек, стоящий в начале.

### Основные операции очереди:

1. **enqueue** — добавление элемента в конец очереди
2. **dequeue** — удаление и возврат элемента из начала очереди
3. **front** — просмотр элемента в начале очереди без его удаления
4. **isEmpty** — проверка, пуста ли очередь

### Реализация очереди в Python

```python
class Queue:
    """
    Реализация очереди с использованием списка Python.
    """
    def __init__(self):
        self.items = []
    
    def enqueue(self, item):
        """Добавляет элемент в конец очереди."""
        self.items.append(item)
    
    def dequeue(self):
        """
        Удаляет и возвращает элемент из начала очереди.
        Вызывает исключение, если очередь пуста.
        """
        if self.is_empty():
            raise Exception("Ошибка: очередь пуста")
        return self.items.pop(0)  # O(n) операция, неэффективно для больших очередей
    
    def front(self):
        """
        Возвращает элемент из начала очереди без его удаления.
        Вызывает исключение, если очередь пуста.
        """
        if self.is_empty():
            raise Exception("Ошибка: очередь пуста")
        return self.items[0]
    
    def is_empty(self):
        """Проверяет, пуста ли очередь."""
        return len(self.items) == 0
    
    def size(self):
        """Возвращает количество элементов в очереди."""
        return len(self.items)
    
    def __str__(self):
        """Возвращает строковое представление очереди."""
        return str(self.items)

# Пример использования
queue = Queue()
queue.enqueue("A")
queue.enqueue("B")
queue.enqueue("C")

print(f"Очередь: {queue}")
print(f"Начало очереди: {queue.front()}")
print(f"Извлекаем элемент: {queue.dequeue()}")
print(f"Очередь после извлечения: {queue}")
```

**Примечание**: Эта реализация очереди неэффективна для больших объемов данных, так как операция `pop(0)` имеет сложность O(n). Для более эффективной реализации можно использовать `collections.deque`.

```python
from collections import deque

class EfficientQueue:
    """
    Эффективная реализация очереди с использованием collections.deque.
    """
    def __init__(self):
        self.items = deque()
    
    def enqueue(self, item):
        """Добавляет элемент в конец очереди. O(1)"""
        self.items.append(item)
    
    def dequeue(self):
        """
        Удаляет и возвращает элемент из начала очереди. O(1)
        Вызывает исключение, если очередь пуста.
        """
        if self.is_empty():
            raise Exception("Ошибка: очередь пуста")
        return self.items.popleft()
    
    def front(self):
        """
        Возвращает элемент из начала очереди без его удаления.
        Вызывает исключение, если очередь пуста.
        """
        if self.is_empty():
            raise Exception("Ошибка: очередь пуста")
        return self.items[0]
    
    def is_empty(self):
        """Проверяет, пуста ли очередь."""
        return len(self.items) == 0
    
    def size(self):
        """Возвращает количество элементов в очереди."""
        return len(self.items)
    
    def __str__(self):
        """Возвращает строковое представление очереди."""
        return str(list(self.items))

# Пример использования
efficient_queue = EfficientQueue()
efficient_queue.enqueue("X")
efficient_queue.enqueue("Y")
efficient_queue.enqueue("Z")

print(f"Эффективная очередь: {efficient_queue}")
print(f"Начало очереди: {efficient_queue.front()}")
print(f"Извлекаем элемент: {efficient_queue.dequeue()}")
print(f"Очередь после извлечения: {efficient_queue}")
```

## Особые виды очередей

### Двусторонняя очередь (Deque)

**Двусторонняя очередь** (Double-ended queue, или deque) — это очередь, которая поддерживает добавление и удаление элементов с обоих концов.

```python
from collections import deque

# Создаем двустороннюю очередь
d = deque()

# Добавляем элементы с обоих концов
d.append("конец")       # добавление справа
d.appendleft("начало")  # добавление слева

# Удаляем элементы с обоих концов
right = d.pop()         # удаление справа
left = d.popleft()      # удаление слева

print(f"Удаленный элемент справа: {right}")
print(f"Удаленный элемент слева: {left}")
```

### Очередь с приоритетом (Priority Queue)

**Очередь с приоритетом** — это очередь, в которой каждый элемент имеет приоритет, и элементы извлекаются в порядке их приоритета, а не в порядке добавления.

```python
import heapq

class PriorityQueue:
    """
    Реализация очереди с приоритетом с использованием модуля heapq.
    """
    def __init__(self):
        self.elements = []
        self.counter = 0  # Для обеспечения стабильности сортировки
    
    def enqueue(self, item, priority=0):
        """
        Добавляет элемент в очередь с заданным приоритетом.
        Элементы с меньшим значением приоритета извлекаются первыми.
        """
        # Используем счетчик для стабильной сортировки элементов с одинаковым приоритетом
        heapq.heappush(self.elements, (priority, self.counter, item))
        self.counter += 1
    
    def dequeue(self):
        """
        Удаляет и возвращает элемент с наивысшим приоритетом
        (наименьшим значением приоритета).
        Вызывает исключение, если очередь пуста.
        """
        if self.is_empty():
            raise Exception("Ошибка: очередь с приоритетом пуста")
        
        # Возвращаем только сам элемент, без приоритета и счетчика
        return heapq.heappop(self.elements)[2]
    
    def peek(self):
        """
        Возвращает элемент с наивысшим приоритетом без его удаления.
        Вызывает исключение, если очередь пуста.
        """
        if self.is_empty():
            raise Exception("Ошибка: очередь с приоритетом пуста")
        
        return self.elements[0][2]
    
    def is_empty(self):
        """Проверяет, пуста ли очередь."""
        return len(self.elements) == 0
    
    def size(self):
        """Возвращает количество элементов в очереди."""
        return len(self.elements)

# Пример использования
pq = PriorityQueue()
pq.enqueue("Обычная задача", 3)
pq.enqueue("Важная задача", 1)
pq.enqueue("Низкоприоритетная задача", 5)
pq.enqueue("Критическая задача", 0)

print("Извлекаем задачи в порядке приоритета:")
while not pq.is_empty():
    print(pq.dequeue())
```

## Алгоритмы на стеках

### 1. Проверка сбалансированности скобок

Алгоритм проверяет, правильно ли расставлены скобки в выражении.

```python
def is_balanced(expression):
    """
    Проверяет, сбалансированы ли скобки в выражении.
    
    Args:
        expression: Строка с выражением, содержащим скобки
        
    Returns:
        True, если скобки сбалансированы, иначе False
    """
    stack = []
    # Определяем пары соответствующих открывающих и закрывающих скобок
    brackets = {')': '(', '}': '{', ']': '['}
    
    for char in expression:
        # Если символ - открывающая скобка, добавляем в стек
        if char in '({[':
            stack.append(char)
        # Если символ - закрывающая скобка
        elif char in ')}]':
            # Если стек пуст или последняя открывающая скобка не соответствует текущей закрывающей
            if not stack or stack.pop() != brackets[char]:
                return False
    
    # Если стек пуст, все скобки сбалансированы
    return len(stack) == 0

# Примеры использования
expressions = [
    "(a + b) * (c - d)",
    "[(a + b) * (c - d)]",
    "({[x + y]})",
    "((a + b) * (c - d)",
    "[(a + b] * (c - d))",
    "({x + y})"
]

for expr in expressions:
    print(f"'{expr}': {'сбалансировано' if is_balanced(expr) else 'несбалансировано'}")
```

### 2. Вычисление выражений в постфиксной записи (обратной польской нотации)

В постфиксной записи операторы следуют за операндами. Например, `2 3 +` эквивалентно инфиксному выражению `2 + 3`.

```python
def evaluate_postfix(expression):
    """
    Вычисляет выражение в постфиксной записи.
    
    Args:
        expression: Строка с выражением в постфиксной записи,
                    где операнды и операторы разделены пробелами
        
    Returns:
        Результат вычисления выражения
    """
    stack = []
    tokens = expression.split()
    
    for token in tokens:
        # Если токен - число, помещаем его в стек
        if token.isdigit() or (token[0] == '-' and token[1:].isdigit()):
            stack.append(float(token))
        # Если токен - оператор, извлекаем операнды из стека, выполняем операцию
        # и помещаем результат обратно в стек
        else:
            if len(stack) < 2:
                raise ValueError(f"Недостаточно операндов для оператора {token}")
            
            # Извлекаем операнды (обратите внимание на порядок)
            b = stack.pop()  # Второй операнд
            a = stack.pop()  # Первый операнд
            
            # Выполняем операцию
            if token == '+':
                stack.append(a + b)
            elif token == '-':
                stack.append(a - b)
            elif token == '*':
                stack.append(a * b)
            elif token == '/':
                if b == 0:
                    raise ValueError("Деление на ноль")
                stack.append(a / b)
            elif token == '^':
                stack.append(a ** b)
            else:
                raise ValueError(f"Неизвестный оператор: {token}")
    
    # Результат должен быть единственным элементом в стеке
    if len(stack) != 1:
        raise ValueError("Некорректное выражение")
    
    return stack[0]

# Примеры использования
postfix_expressions = [
    "2 3 +",           # 2 + 3 = 5
    "5 2 * 3 +",       # 5 * 2 + 3 = 13
    "3 4 2 * +",       # 3 + 4 * 2 = 11
    "3 4 + 2 *",       # (3 + 4) * 2 = 14
    "5 1 2 + 4 * + 3 -"  # 5 + ((1 + 2) * 4) - 3 = 14
]

for expr in postfix_expressions:
    try:
        result = evaluate_postfix(expr)
        print(f"'{expr}' = {result}")
    except ValueError as e:
        print(f"Ошибка при вычислении '{expr}': {e}")
```

### 3. Преобразование инфиксного выражения в постфиксное

Преобразование выражения из привычной инфиксной записи (например, `a + b * c`) в постфиксную (`a b c * +`).

```python
def infix_to_postfix(expression):
    """
    Преобразует выражение из инфиксной записи в постфиксную.
    
    Args:
        expression: Строка с выражением в инфиксной записи
        
    Returns:
        Строка с выражением в постфиксной записи
    """
    # Определяем приоритеты операторов
    precedence = {'+': 1, '-': 1, '*': 2, '/': 2, '^': 3}
    
    stack = []
    output = []
    
    # Обрабатываем каждый символ в выражении
    i = 0
    while i < len(expression):
        char = expression[i]
        
        # Пропускаем пробелы
        if char.isspace():
            i += 1
            continue
        
        # Если символ - число или переменная, добавляем в выходную строку
        if char.isalnum():
            # Собираем многозначное число
            token = char
            j = i + 1
            while j < len(expression) and expression[j].isdigit():
                token += expression[j]
                j += 1
            output.append(token)
            i = j
            continue
        
        # Если символ - открывающая скобка, помещаем в стек
        if char == '(':
            stack.append(char)
        
        # Если символ - закрывающая скобка, выталкиваем операторы из стека
        # до соответствующей открывающей скобки
        elif char == ')':
            while stack and stack[-1] != '(':
                output.append(stack.pop())
            
            # Удаляем открывающую скобку из стека
            if stack and stack[-1] == '(':
                stack.pop()
            else:
                raise ValueError("Несбалансированные скобки")
        
        # Если символ - оператор
        elif char in precedence:
            # Выталкиваем операторы с большим или равным приоритетом
            while (stack and stack[-1] != '(' and
                  stack[-1] in precedence and
                  (precedence[stack[-1]] > precedence[char] or
                   (precedence[stack[-1]] == precedence[char] and char != '^'))):
                output.append(stack.pop())
            
            # Помещаем текущий оператор в стек
            stack.append(char)
        
        i += 1
    
    # Выталкиваем оставшиеся операторы из стека
    while stack:
        if stack[-1] == '(':
            raise ValueError("Несбалансированные скобки")
        output.append(stack.pop())
    
    # Собираем результат
    return ' '.join(output)

# Примеры использования
infix_expressions = [
    "a + b",
    "a + b * c",
    "(a + b) * c",
    "a + b * c + d",
    "a * (b + c) - d",
    "a * b ^ c + d"
]

for expr in infix_expressions:
    try:
        postfix = infix_to_postfix(expr)
        print(f"Инфиксная запись: '{expr}'")
        print(f"Постфиксная запись: '{postfix}'")
        print()
    except ValueError as e:
        print(f"Ошибка при преобразовании '{expr}': {e}")
```

### 4. Нахождение следующего большего элемента

Для каждого элемента в массиве находим ближайший больший элемент справа от него.

```python
def next_greater_element(arr):
    """
    Находит для каждого элемента массива ближайший больший элемент справа.
    
    Args:
        arr: Список чисел
        
    Returns:
        Список, где i-й элемент - это ближайший больший элемент справа от arr[i],
        или -1, если такого элемента нет
    """
    n = len(arr)
    result = [-1] * n  # По умолчанию для всех элементов результат -1
    stack = []  # Стек для хранения индексов
    
    # Проходим по массиву справа налево
    for i in range(n - 1, -1, -1):
        # Пока стек не пуст и текущий элемент больше или равен элементу на вершине стека
        while stack and arr[i] >= arr[stack[-1]]:
            stack.pop()  # Удаляем элементы, которые меньше или равны текущему
        
        # Если стек не пуст, верхний элемент - ближайший больший справа
        if stack:
            result[i] = arr[stack[-1]]
        
        # Добавляем индекс текущего элемента в стек
        stack.append(i)
    
    return result

# Пример использования
arr = [4, 6, 3, 2, 8, 1, 9, 9, 7]
result = next_greater_element(arr)

print("Массив:", arr)
print("Следующий больший элемент:", result)

# Вывод в более читаемом формате
for i, num in enumerate(arr):
    next_greater = result[i]
    print(f"{num} --> {next_greater}")
```

## Алгоритмы на очередях

### 1. Обход дерева в ширину (Breadth-First Search)

Алгоритм обхода дерева или графа, который исследует все вершины на текущем уровне перед переходом на следующий уровень.

```python
from collections import deque

class TreeNode:
    """Узел бинарного дерева."""
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def breadth_first_traversal(root):
    """
    Выполняет обход дерева в ширину (BFS).
    
    Args:
        root: Корневой узел дерева
        
    Returns:
        Список значений узлов в порядке их посещения
    """
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        # Извлекаем узел из очереди
        node = queue.popleft()
        result.append(node.val)
        
        # Добавляем дочерние узлы в очередь
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
    
    return result

# Создаем тестовое дерево:
#       1
#      / \
#     2   3
#    / \   \
#   4   5   6
root = TreeNode(1)
root.left = TreeNode(2)
root.right = TreeNode(3)
root.left.left = TreeNode(4)
root.left.right = TreeNode(5)
root.right.right = TreeNode(6)

bfs_result = breadth_first_traversal(root)
print("Обход дерева в ширину (BFS):", bfs_result)
```

### 2. Печать дерева по уровням

Печать узлов бинарного дерева уровень за уровнем, с каждым уровнем на отдельной строке.

```python
def print_tree_by_levels(root):
    """
    Печатает дерево по уровням.
    
    Args:
        root: Корневой узел дерева
    """
    if not root:
        print("Дерево пусто")
        return
    
    queue = deque([root])
    current_level = 1  # Количество узлов на текущем уровне
    next_level = 0     # Количество узлов на следующем уровне
    level_number = 1   # Номер текущего уровня
    
    print(f"Уровень {level_number}:", end=" ")
    
    while queue:
        node = queue.popleft()
        current_level -= 1
        
        print(node.val, end=" ")
        
        if node.left:
            queue.append(node.left)
            next_level += 1
        
        if node.right:
            queue.append(node.right)
            next_level += 1
        
        # Если текущий уровень закончился, переходим к следующему
        if current_level == 0:
            print()  # Переходим на новую строку
            
            if next_level > 0:
                level_number += 1
                print(f"Уровень {level_number}:", end=" ")
                current_level = next_level
                next_level = 0

# Используем то же тестовое дерево
print_tree_by_levels(root)
```

### 3. Нахождение кратчайшего пути в лабиринте

Алгоритм нахождения кратчайшего пути из стартовой точки в конечную в двумерном лабиринте с использованием BFS.

```python
def shortest_path_in_maze(maze, start, end):
    """
    Находит кратчайший путь в лабиринте с помощью BFS.
    
    Args:
        maze: Двумерный массив, где 0 - проходимая клетка, 1 - стена
        start: Кортеж (row, col) с координатами начальной точки
        end: Кортеж (row, col) с координатами конечной точки
        
    Returns:
        Длина кратчайшего пути и список координат пути или (None, None),
        если путь не найден
    """
    if not maze or not maze[0]:
        return None, None
    
    rows, cols = len(maze), len(maze[0])
    
    # Проверяем корректность входных координат
    for r, c in [start, end]:
        if not (0 <= r < rows and 0 <= c < cols):
            return None, None
        if maze[r][c] == 1:  # Стена
            return None, None
    
    # Возможные направления движения (вверх, вправо, вниз, влево)
    directions = [(-1, 0), (0, 1), (1, 0), (0, -1)]
    
    # Очередь для BFS, храним кортежи (row, col, distance, path)
    queue = deque([(start[0], start[1], 0, [start])])
    
    # Множество посещенных клеток
    visited = {start}
    
    while queue:
        row, col, distance, path = queue.popleft()
        
        # Если достигли конечной точки, возвращаем результат
        if (row, col) == end:
            return distance, path
        
        # Проверяем все возможные направления
        for dr, dc in directions:
            new_row, new_col = row + dr, col + dc
            
            # Проверяем, что новые координаты корректны, клетка проходима и не посещена
            if (0 <= new_row < rows and 0 <= new_col < cols and
                maze[new_row][new_col] == 0 and
                (new_row, new_col) not in visited):
                
                # Добавляем новую клетку в очередь
                new_path = path + [(new_row, new_col)]
                queue.append((new_row, new_col, distance + 1, new_path))
                visited.add((new_row, new_col))
    
    # Если путь не найден
    return None, None

# Пример использования
maze = [
    [0, 0, 1, 0, 0, 0],
    [0, 0, 1, 0, 1, 0],
    [0, 0, 0, 0, 1, 0],
    [0, 1, 1, 1, 1, 0],
    [0, 0, 0, 0, 0, 0]
]

start = (0, 0)
end = (4, 5)

distance, path = shortest_path_in_maze(maze, start, end)

if distance is not None:
    print(f"Длина кратчайшего пути: {distance}")
    print(f"Путь: {path}")
else:
    print("Путь не найден")

# Визуализация лабиринта и пути
def visualize_maze_with_path(maze, path):
    """Печатает лабиринт с отмеченным путем."""
    if not path:
        print("Путь не найден")
        return
    
    # Создаем копию лабиринта для визуализации
    visual_maze = []
    for row in maze:
        visual_maze.append(row.copy())
    
    # Отмечаем путь в лабиринте
    for r, c in path:
        visual_maze[r][c] = '*'
    
    # Отмечаем начальную и конечную точки
    start_r, start_c = path[0]
    end_r, end_c = path[-1]
    visual_maze[start_r][start_c] = 'S'
    visual_maze[end_r][end_c] = 'E'
    
    # Печатаем лабиринт
    for row in visual_maze:
        row_str = ''
        for cell in row:
            if cell == 0:
                row_str += ' . '  # Проходимая клетка
            elif cell == 1:
                row_str += ' # '  # Стена
            elif cell == '*':
                row_str += ' * '  # Путь
            elif cell == 'S':
                row_str += ' S '  # Старт
            elif cell == 'E':
                row_str += ' E '  # Финиш
            else:
                row_str += f' {cell} '
        print(row_str)

print("\nВизуализация лабиринта и пути:")
visualize_maze_with_path(maze, path)
```

### 4. Топологическая сортировка с использованием очереди (алгоритм Кана)

Алгоритм для сортировки ориентированного ациклического графа (DAG) так, чтобы для каждого ребра u -> v, u идет перед v в сортировке.

```python
from collections import defaultdict, deque

def topological_sort(graph):
    """
    Выполняет топологическую сортировку графа с помощью алгоритма Кана.
    
    Args:
        graph: Словарь, представляющий граф в виде {вершина: [соседи]}
        
    Returns:
        Список вершин в топологическом порядке или None, если граф содержит цикл
    """
    # Создаем копию графа, чтобы не изменять оригинал
    graph = graph.copy()
    
    # Вычисляем входящие степени для всех вершин
    in_degree = defaultdict(int)
    for node in graph:
        for neighbor in graph[node]:
            in_degree[neighbor] += 1
    
    # Добавляем в очередь вершины с нулевой входящей степенью
    queue = deque()
    for node in graph:
        if in_degree[node] == 0:
            queue.append(node)
    
    # Список для хранения результата
    result = []
    
    # Обрабатываем вершины
    while queue:
        node = queue.popleft()
        result.append(node)
        
        # Уменьшаем входящие степени соседей
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            
            # Если входящая степень стала нулевой, добавляем в очередь
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    # Если обработаны не все вершины, граф содержит цикл
    if len(result) != len(graph):
        return None
    
    return result

# Пример использования
# Граф зависимостей для задач: {задача: [зависимости]}
task_dependencies = {
    'A': [],
    'B': ['A'],
    'C': ['A'],
    'D': ['B', 'C'],
    'E': ['D'],
    'F': ['C']
}

# Преобразуем граф зависимостей в граф предшественников: {задача: [предшественники]}
graph = {task: [] for task in task_dependencies}
for task, deps in task_dependencies.items():
    for dep in deps:
        graph[dep].append(task)

topological_order = topological_sort(graph)

if topological_order:
    print("Топологический порядок задач:")
    print(" -> ".join(topological_order))
else:
    print("Граф содержит цикл, топологическая сортировка невозможна")
```

### 5. Имитация очереди обработки задач

Разработка простой системы обработки задач с различными приоритетами, имитирующей реальные сценарии в веб-разработке.

```python
import time
import threading
import random
from queue import PriorityQueue

class TaskProcessor:
    """
    Имитация системы обработки задач с различными приоритетами.
    """
    def __init__(self, num_workers=2):
        """
        Инициализирует обработчик задач с заданным количеством рабочих потоков.
        
        Args:
            num_workers: Количество рабочих потоков
        """
        self.task_queue = PriorityQueue()
        self.num_workers = num_workers
        self.workers = []
        self.running = False
        self.task_counter = 0
        self.lock = threading.Lock()
    
    def add_task(self, task_func, priority=2, *args, **kwargs):
        """
        Добавляет задачу в очередь.
        
        Args:
            task_func: Функция, которую нужно выполнить
            priority: Приоритет задачи (0 - высший, 5 - низший)
            *args, **kwargs: Аргументы для функции
        """
        with self.lock:
            self.task_counter += 1
            task_id = self.task_counter
        
        # Элементы в очереди с приоритетом: (приоритет, время добавления, id задачи, функция, аргументы)
        # Время добавления и id задачи нужны для стабильной сортировки
        self.task_queue.put((priority, time.time(), task_id, task_func, args, kwargs))
        print(f"Задача #{task_id} добавлена в очередь с приоритетом {priority}")
    
    def worker(self, worker_id):
        """
        Функция рабочего потока, обрабатывающего задачи из очереди.
        
        Args:
            worker_id: Идентификатор рабочего потока
        """
        print(f"Рабочий поток #{worker_id} запущен")
        
        while self.running:
            try:
                # Извлекаем задачу из очереди с таймаутом
                priority, _, task_id, task_func, args, kwargs = self.task_queue.get(timeout=1)
                
                # Обрабатываем задачу
                print(f"Рабочий #{worker_id} начал обработку задачи #{task_id} (приоритет {priority})")
                start_time = time.time()
                
                try:
                    result = task_func(*args, **kwargs)
                    print(f"Задача #{task_id} выполнена за {time.time() - start_time:.2f} сек, результат: {result}")
                except Exception as e:
                    print(f"Ошибка при выполнении задачи #{task_id}: {e}")
                
                # Отмечаем задачу как выполненную
                self.task_queue.task_done()
            
            except Exception:
                # Таймаут или другая ошибка, проверяем, нужно ли продолжать работу
                pass
        
        print(f"Рабочий поток #{worker_id} завершен")
    
    def start(self):
        """Запускает обработчик задач."""
        if self.running:
            return
        
        self.running = True
        
        # Запускаем рабочие потоки
        for i in range(self.num_workers):
            worker = threading.Thread(target=self.worker, args=(i + 1,))
            worker.daemon = True
            worker.start()
            self.workers.append(worker)
        
        print(f"Обработчик задач запущен с {self.num_workers} рабочими потоками")
    
    def stop(self):
        """Останавливает обработчик задач."""
        self.running = False
        
        # Ждем завершения всех рабочих потоков
        for worker in self.workers:
            worker.join(timeout=2)
        
        self.workers = []
        print("Обработчик задач остановлен")

# Пример использования
def sample_task(name, duration):
    """Пример задачи, которая выполняется заданное время."""
    # Имитируем работу с помощью sleep
    time.sleep(duration)
    return f"Задача '{name}' выполнена"

# Создаем и запускаем обработчик задач
processor = TaskProcessor(num_workers=2)
processor.start()

try:
    # Добавляем задачи с разными приоритетами
    processor.add_task(sample_task, 2, "Обычная задача", 1.0)
    processor.add_task(sample_task, 0, "Критическая задача", 0.5)
    processor.add_task(sample_task, 1, "Важная задача", 1.5)
    processor.add_task(sample_task, 3, "Неприоритетная задача", 0.8)
    
    # Даем время на обработку задач
    time.sleep(5)
    
    # Добавляем еще несколько задач
    processor.add_task(sample_task, 0, "Еще одна критическая задача", 0.3)
    processor.add_task(sample_task, 2, "Еще одна обычная задача", 0.7)
    
    # Даем время на обработку оставшихся задач
    time.sleep(5)

finally:
    # Останавливаем обработчик задач
    processor.stop()
```

## Практическое применение в веб-разработке

### 1. Обработка последовательности запросов API с ограничением скорости

```python
import time
import threading
from collections import deque

class RateLimiter:
    """
    Ограничитель скорости для API-запросов, использующий алгоритм токенного ведра.
    """
    def __init__(self, rate_limit, time_window=60):
        """
        Инициализирует ограничитель скорости.
        
        Args:
            rate_limit: Максимальное количество запросов в заданное окно времени
            time_window: Окно времени в секундах (по умолчанию 60 секунд)
        """
        self.rate_limit = rate_limit
        self.time_window = time_window
        self.request_times = deque()
        self.lock = threading.Lock()
    
    def can_make_request(self):
        """
        Проверяет, можно ли выполнить запрос, не превышая ограничение скорости.
        
        Returns:
            True, если запрос разрешен, иначе False
        """
        current_time = time.time()
        
        with self.lock:
            # Удаляем устаревшие записи о запросах
            while self.request_times and self.request_times[0] < current_time - self.time_window:
                self.request_times.popleft()
            
            # Проверяем, не превышено ли ограничение
            if len(self.request_times) < self.rate_limit:
                self.request_times.append(current_time)
                return True
            else:
                return False
    
    def wait_for_request(self, timeout=None):
        """
        Ожидает, пока запрос не будет разрешен.
        
        Args:
            timeout: Максимальное время ожидания в секундах
            
        Returns:
            True, если запрос разрешен, False, если превышен таймаут
        """
        start_time = time.time()
        
        while True:
            if self.can_make_request():
                return True
            
            # Проверяем таймаут
            if timeout is not None and time.time() - start_time > timeout:
                return False
            
            # Ждем немного перед следующей попыткой
            time.sleep(0.1)

class APIClient:
    """
    Клиент API с ограничением скорости запросов.
    """
    def __init__(self, rate_limit=5, time_window=10):
        """
        Инициализирует клиент API.
        
        Args:
            rate_limit: Максимальное количество запросов в заданное окно времени
            time_window: Окно времени в секундах
        """
        self.rate_limiter = RateLimiter(rate_limit, time_window)
        self.request_queue = deque()
        self.processing = False
        self.lock = threading.Lock()
    
    def make_request(self, endpoint, params=None, callback=None):
        """
        Добавляет запрос в очередь и запускает обработку, если она не запущена.
        
        Args:
            endpoint: Конечная точка API
            params: Параметры запроса
            callback: Функция обратного вызова для обработки результата
        """
        # Создаем запись о запросе
        request = {
            'endpoint': endpoint,
            'params': params or {},
            'callback': callback,
            'time': time.time()
        }
        
        # Добавляем запрос в очередь
        with self.lock:
            self.request_queue.append(request)
            should_start = not self.processing
        
        # Запускаем обработку, если она еще не запущена
        if should_start:
            threading.Thread(target=self._process_queue, daemon=True).start()
    
    def _process_queue(self):
        """Обрабатывает очередь запросов с учетом ограничения скорости."""
        with self.lock:
            self.processing = True
        
        try:
            while True:
                # Проверяем, есть ли запросы в очереди
                with self.lock:
                    if not self.request_queue:
                        self.processing = False
                        break
                
                # Ожидаем разрешения на выполнение запроса
                if not self.rate_limiter.wait_for_request(timeout=60):
                    print("Превышен таймаут ожидания разрешения запроса")
                    continue
                
                # Получаем запрос из очереди
                with self.lock:
                    if not self.request_queue:
                        self.processing = False
                        break
                    request = self.request_queue.popleft()
                
                # Выполняем запрос (имитация)
                self._execute_request(request)
        
        except Exception as e:
            print(f"Ошибка при обработке очереди запросов: {e}")
        
        finally:
            with self.lock:
                self.processing = False
    
    def _execute_request(self, request):
        """
        Выполняет запрос API (имитация).
        
        Args:
            request: Информация о запросе
        """
        endpoint = request['endpoint']
        params = request['params']
        
        # Имитируем задержку сети
        delay = 0.2 + random.random() * 0.3
        time.sleep(delay)
        
        # Имитируем ответ API
        response = {
            'endpoint': endpoint,
            'params': params,
            'status': 'success',
            'data': f"Данные для {endpoint}",
            'time_taken': delay
        }
        
        print(f"Выполнен запрос к {endpoint} с параметрами {params}, время: {delay:.2f} сек")
        
        # Вызываем функцию обратного вызова, если она указана
        if request['callback']:
            try:
                request['callback'](response)
            except Exception as e:
                print(f"Ошибка в функции обратного вызова: {e}")

# Функция обратного вызова для обработки ответа
def handle_response(response):
    print(f"Обработка ответа от {response['endpoint']}: {response['data']}")

# Пример использования
api_client = APIClient(rate_limit=3, time_window=5)

# Добавляем несколько запросов в очередь
endpoints = ['/users', '/products', '/orders', '/categories', '/settings', '/auth']
for i, endpoint in enumerate(endpoints):
    api_client.make_request(endpoint, {'page': i + 1}, handle_response)
    print(f"Запрос к {endpoint} добавлен в очередь")

# Даем время на обработку всех запросов
time.sleep(10)
```

### 2. Управление историей изменений и отмена действий (Undo/Redo)

```python
class TextEditor:
    """
    Простой текстовый редактор с поддержкой отмены и повтора действий.
    """
    def __init__(self):
        self.text = ""
        self.undo_stack = []  # Стек для отмены действий
        self.redo_stack = []  # Стек для повтора действий
    
    def insert(self, position, text):
        """
        Вставляет текст в указанную позицию.
        
        Args:
            position: Позиция для вставки
            text: Текст для вставки
        """
        # Сохраняем текущее состояние для возможности отмены
        self.undo_stack.append(("delete", position, len(text)))
        
        # Выполняем вставку
        self.text = self.text[:position] + text + self.text[position:]
        
        # Очищаем стек повтора, т.к. выполнено новое действие
        self.redo_stack = []
        
        return self.text
    
    def delete(self, position, length):
        """
        Удаляет текст указанной длины, начиная с указанной позиции.
        
        Args:
            position: Начальная позиция
            length: Длина удаляемого текста
        """
        # Проверяем корректность позиции и длины
        if position < 0 or position + length > len(self.text):
            raise ValueError("Некорректная позиция или длина")
        
        # Сохраняем удаляемый текст для возможности отмены
        deleted_text = self.text[position:position + length]
        self.undo_stack.append(("insert", position, deleted_text))
        
        # Выполняем удаление
        self.text = self.text[:position] + self.text[position + length:]
        
        # Очищаем стек повтора
        self.redo_stack = []
        
        return self.text
    
    def replace(self, position, length, new_text):
        """
        Заменяет текст указанной длины новым текстом.
        
        Args:
            position: Начальная позиция
            length: Длина заменяемого текста
            new_text: Новый текст
        """
        # Выполняем как комбинацию удаления и вставки
        self.delete(position, length)
        return self.insert(position, new_text)
    
    def undo(self):
        """
        Отменяет последнее действие.
        
        Returns:
            Текст после отмены действия
        """
        if not self.undo_stack:
            print("Нечего отменять")
            return self.text
        
        # Извлекаем последнее действие из стека отмены
        action = self.undo_stack.pop()
        
        # Выполняем обратное действие
        if action[0] == "insert":
            # Действие было удалением, теперь нужно вставить
            position, text = action[1], action[2]
            
            # Сохраняем в стек повтора
            self.redo_stack.append(("delete", position, len(text)))
            
            # Выполняем вставку для отмены удаления
            self.text = self.text[:position] + text + self.text[position:]
        
        elif action[0] == "delete":
            # Действие было вставкой, теперь нужно удалить
            position, length = action[1], action[2]
            
            # Сохраняем в стек повтора
            deleted_text = self.text[position:position + length]
            self.redo_stack.append(("insert", position, deleted_text))
            
            # Выполняем удаление для отмены вставки
            self.text = self.text[:position] + self.text[position + length:]
        
        return self.text
    
    def redo(self):
        """
        Повторяет последнее отмененное действие.
        
        Returns:
            Текст после повтора действия
        """
        if not self.redo_stack:
            print("Нечего повторять")
            return self.text
        
        # Извлекаем последнее отмененное действие
        action = self.redo_stack.pop()
        
        # Выполняем действие
        if action[0] == "insert":
            # Нужно вставить текст
            position, text = action[1], action[2]
            
            # Сохраняем в стек отмены
            self.undo_stack.append(("delete", position, len(text)))
            
            # Выполняем вставку
            self.text = self.text[:position] + text + self.text[position:]
        
        elif action[0] == "delete":
            # Нужно удалить текст
            position, length = action[1], action[2]
            
            # Сохраняем в стек отмены
            deleted_text = self.text[position:position + length]
            self.undo_stack.append(("insert", position, deleted_text))
            
            # Выполняем удаление
            self.text = self.text[:position] + self.text[position + length:]
        
        return self.text

# Пример использования
editor = TextEditor()

print("Начальный текст:", editor.text)

# Выполняем несколько действий
editor.insert(0, "Привет, ")
print("После вставки 'Привет, ':", editor.text)

editor.insert(8, "мир!")
print("После вставки 'мир!':", editor.text)

editor.replace(0, 7, "Здравствуй, ")
print("После замены 'Привет' на 'Здравствуй':", editor.text)

# Отменяем действия
editor.undo()
print("После отмены (замены):", editor.text)

editor.undo()
print("После отмены (вставки 'мир!'):", editor.text)

# Повторяем отмененные действия
editor.redo()
print("После повтора (вставки 'мир!'):", editor.text)

# Выполняем новое действие
editor.insert(8, "Python!")
print("После вставки 'Python!':", editor.text)

# Пытаемся повторить отмененное действие (не сработает, т.к. был новый ввод)
editor.redo()
print("После попытки повтора:", editor.text)
```

### 3. Обработка и валидация вложенных форм

```python
class FormValidator:
    """
    Валидатор форм, использующий стек для обработки вложенных форм и полей.
    """
    def __init__(self):
        self.errors = {}
    
    def validate(self, form_data, schema):
        """
        Валидирует данные формы по заданной схеме.
        
        Args:
            form_data: Словарь с данными формы
            schema: Схема валидации
            
        Returns:
            (bool, dict): Флаг успеха и словарь с ошибками
        """
        self.errors = {}
        
        # Стек для отслеживания пути к текущему полю
        path_stack = []
        
        # Рекурсивная функция для обработки вложенных полей
        def validate_object(data, schema, path):
            if not isinstance(data, dict):
                self._add_error(path, "Должен быть объектом")
                return
            
            # Проверяем обязательные поля
            for field_name, field_schema in schema.items():
                field_path = path + [field_name]
                
                # Если поле обязательное и отсутствует
                if field_schema.get('required', False) and field_name not in data:
                    self._add_error(field_path, "Обязательное поле")
                    continue
                
                # Если поле отсутствует, но не обязательное
                if field_name not in data:
                    continue
                
                field_value = data[field_name]
                field_type = field_schema.get('type')
                
                # Валидируем в зависимости от типа
                if field_type == 'string':
                    if not isinstance(field_value, str):
                        self._add_error(field_path, "Должно быть строкой")
                    elif 'minLength' in field_schema and len(field_value) < field_schema['minLength']:
                        self._add_error(field_path, f"Минимальная длина: {field_schema['minLength']}")
                    elif 'maxLength' in field_schema and len(field_value) > field_schema['maxLength']:
                        self._add_error(field_path, f"Максимальная длина: {field_schema['maxLength']}")
                
                elif field_type == 'number':
                    if not isinstance(field_value, (int, float)):
                        self._add_error(field_path, "Должно быть числом")
                    elif 'min' in field_schema and field_value < field_schema['min']:
                        self._add_error(field_path, f"Минимальное значение: {field_schema['min']}")
                    elif 'max' in field_schema and field_value > field_schema['max']:
                        self._add_error(field_path, f"Максимальное значение: {field_schema['max']}")
                
                elif field_type == 'boolean':
                    if not isinstance(field_value, bool):
                        self._add_error(field_path, "Должно быть логическим значением")
                
                elif field_type == '_1.arrays':
                    if not isinstance(field_value, list):
                        self._add_error(field_path, "Должно быть массивом")
                    else:
                        # Валидируем каждый элемент массива
                        for i, item in enumerate(field_value):
                            item_path = field_path + [i]
                            
                            if 'items' in field_schema:
                                item_schema = field_schema['items']
                                
                                if item_schema.get('type') == 'object':
                                    # Валидируем вложенный объект
                                    path_stack.append(item_path)
                                    validate_object(item, item_schema.get('properties', {}), item_path)
                                    path_stack.pop()
                                else:
                                    # Валидируем примитивный тип
                                    self._validate_primitive(item, item_schema, item_path)
                
                elif field_type == 'object':
                    # Валидируем вложенный объект
                    path_stack.append(field_path)
                    validate_object(field_value, field_schema.get('properties', {}), field_path)
                    path_stack.pop()
        
        # Запускаем валидацию с корневым объектом
        validate_object(form_data, schema, [])
        
        return len(self.errors) == 0, self.errors
    
    def _validate_primitive(self, value, schema, path):
        """Валидирует примитивное значение по схеме."""
        field_type = schema.get('type')
        
        if field_type == 'string':
            if not isinstance(value, str):
                self._add_error(path, "Должно быть строкой")
                return
            
            if 'minLength' in schema and len(value) < schema['minLength']:
                self._add_error(path, f"Минимальная длина: {schema['minLength']}")
            if 'maxLength' in schema and len(value) > schema['maxLength']:
                self._add_error(path, f"Максимальная длина: {schema['maxLength']}")
        
        elif field_type == 'number':
            if not isinstance(value, (int, float)):
                self._add_error(path, "Должно быть числом")
                return
            
            if 'min' in schema and value < schema['min']:
                self._add_error(path, f"Минимальное значение: {schema['min']}")
            if 'max' in schema and value > schema['max']:
                self._add_error(path, f"Максимальное значение: {schema['max']}")
    
    def _add_error(self, path, message):
        """Добавляет ошибку по указанному пути."""
        # Преобразуем путь в строку для хранения в словаре ошибок
        path_str = '.'.join(str(p) for p in path)
        self.errors[path_str] = message

# Пример использования
# Схема валидации для формы заказа
order_schema = {
    'customer': {
        'type': 'object',
        'required': True,
        'properties': {
            'name': {'type': 'string', 'required': True, 'minLength': 2},
            'email': {'type': 'string', 'required': True},
            'phone': {'type': 'string', 'required': False}
        }
    },
    'items': {
        'type': '_1.arrays',
        'required': True,
        'items': {
            'type': 'object',
            'properties': {
                'product_id': {'type': 'number', 'required': True},
                'quantity': {'type': 'number', 'required': True, 'min': 1},
                'options': {
                    'type': '_1.arrays',
                    'required': False,
                    'items': {
                        'type': 'object',
                        'properties': {
                            'name': {'type': 'string', 'required': True},
                            'value': {'type': 'string', 'required': True}
                        }
                    }
                }
            }
        }
    },
    'shipping_address': {
        'type': 'object',
        'required': True,
        'properties': {
            'street': {'type': 'string', 'required': True},
            'city': {'type': 'string', 'required': True},
            'zip': {'type': 'string', 'required': True},
            'country': {'type': 'string', 'required': True}
        }
    },
    'payment_method': {'type': 'string', 'required': True}
}

# Тестовые данные формы заказа (с ошибками)
order_data = {
    'customer': {
        'name': 'A',  # Слишком короткое имя
        'email': 'example@example.com'
        # Отсутствует необязательное поле 'phone'
    },
    'items': [
        {
            'product_id': 101,
            'quantity': 2,
            'options': [
                {'name': 'Color', 'value': 'Red'},
                {'name': 'Size', 'value': 'L'}
            ]
        },
        {
            'product_id': 102,
            'quantity': 0,  # Количество должно быть не меньше 1
            'options': [
                {'name': 'Color', 'value': 'Blue'}
            ]
        },
        {
            'product_id': "103",  # Должно быть числом
            'quantity': 1
        }
    ],
    'shipping_address': {
        'street': '123 Main St',
        'city': 'New York',
        # Отсутствует обязательное поле 'zip'
        'country': 'USA'
    },
    'payment_method': 'credit_card'
}

# Валидируем форму
validator = FormValidator()
is_valid, errors = validator.validate(order_data, order_schema)

print(f"Форма валидна: {is_valid}")
if not is_valid:
    print("Ошибки:")
    for path, message in errors.items():
        print(f"  {path}: {message}")
```

### 4. Обработка медиа-контента с использованием пайплайна

```python
from collections import deque

class MediaProcessingPipeline:
    """
    Пайплайн для обработки медиа-контента (изображений, видео и т.д.),
    использующий очередь для последовательной обработки задач.
    """
    def __init__(self, max_concurrent_tasks=2):
        self.tasks_queue = deque()
        self.processing_queue = deque()
        self.completed_tasks = []
        self.max_concurrent_tasks = max_concurrent_tasks
    
    def add_task(self, media_type, media_id, operations):
        """
        Добавляет задачу в очередь обработки.
        
        Args:
            media_type: Тип медиа (image, video, audio)
            media_id: Идентификатор медиа
            operations: Список операций для выполнения
        """
        task = {
            'id': f"{media_type}_{media_id}",
            'media_type': media_type,
            'media_id': media_id,
            'operations': operations,
            'status': 'queued',
            'results': {}
        }
        
        self.tasks_queue.append(task)
        print(f"Задача {task['id']} добавлена в очередь")
        
        # Запускаем процесс обработки, если возможно
        self._process_next_tasks()
    
    def _process_next_tasks(self):
        """Запускает обработку следующих задач из очереди."""
        # Проверяем, можем ли запустить новые задачи
        while (len(self.processing_queue) < self.max_concurrent_tasks and
               self.tasks_queue):
            # Извлекаем задачу из очереди ожидания
            task = self.tasks_queue.popleft()
            task['status'] = 'processing'
            
            # Добавляем в очередь обработки
            self.processing_queue.append(task)
            
            # Запускаем обработку (в реальном приложении здесь был бы асинхронный код)
            print(f"Начинаем обработку задачи {task['id']}")
            self._process_task(task)
    
    def _process_task(self, task):
        """
        Обрабатывает задачу, выполняя последовательно все операции.
        В реальном приложении это был бы асинхронный процесс.
        
        Args:
            task: Задача для обработки
        """
        # Имитируем обработку каждой операции
        for op in task['operations']:
            print(f"  Выполняем операцию '{op}' для задачи {task['id']}")
            
            # Имитируем результат операции
            if op == 'resize':
                task['results'][op] = {'width': 800, 'height': 600}
            elif op == 'crop':
                task['results'][op] = {'x': 0, 'y': 0, 'width': 800, 'height': 600}
            elif op == 'watermark':
                task['results'][op] = {'position': 'bottom-right'}
            elif op == 'transcode':
                task['results'][op] = {'format': 'mp4', 'quality': 'high'}
            else:
                task['results'][op] = {'status': 'completed'}
            
            # В реальном приложении здесь была бы задержка
            time.sleep(0.5)
        
        # Завершаем обработку задачи
        self._complete_task(task)
    
    def _complete_task(self, task):
        """
        Завершает обработку задачи и запускает следующую.
        
        Args:
            task: Обработанная задача
        """
        # Удаляем задачу из очереди обработки
        try:
            self.processing_queue.remove(task)
        except ValueError:
            pass
        
        # Обновляем статус и сохраняем результат
        task['status'] = 'completed'
        self.completed_tasks.append(task)
        
        print(f"Задача {task['id']} завершена")
        
        # Запускаем следующие задачи
        self._process_next_tasks()
    
    def get_status(self, task_id):
        """
        Возвращает статус задачи.
        
        Args:
            task_id: Идентификатор задачи
            
        Returns:
            Словарь с информацией о задаче или None, если задача не найдена
        """
        # Проверяем задачи в очереди ожидания
        for task in self.tasks_queue:
            if task['id'] == task_id:
                return {'id': task_id, 'status': task['status']}
        
        # Проверяем задачи в процессе обработки
        for task in self.processing_queue:
            if task['id'] == task_id:
                return {'id': task_id, 'status': task['status']}
        
        # Проверяем завершенные задачи
        for task in self.completed_tasks:
            if task['id'] == task_id:
                return {
                    'id': task_id,
                    'status': task['status'],
                    'results': task['results']
                }
        
        return None

# Пример использования
pipeline = MediaProcessingPipeline(max_concurrent_tasks=2)

# Добавляем задачи на обработку изображений
pipeline.add_task('image', '001', ['resize', 'crop', 'watermark'])
pipeline.add_task('image', '002', ['resize', 'watermark'])

# Добавляем задачу на обработку видео
pipeline.add_task('video', '001', ['transcode', 'watermark'])

# Проверяем статус через некоторое время
time.sleep(3)

task_ids = ['image_001', 'image_002', 'video_001']
for task_id in task_ids:
    status = pipeline.get_status(task_id)
    if status:
        print(f"Статус задачи {task_id}: {status['status']}")
    else:
        print(f"Задача {task_id} не найдена")

# Даем время на завершение всех задач
time.sleep(5)

print("\nОкончательный статус задач:")
for task_id in task_ids:
    status = pipeline.get_status(task_id)
    if status:
        print(f"Задача {task_id}: {status['status']}")
        if 'results' in status:
            print(f"  Результаты: {status['results']}")
```

## Заключение

Стеки и очереди — это мощные структуры данных, которые находят широкое применение в программировании и особенно в веб-разработке. Они позволяют эффективно организовывать и обрабатывать данные в различных сценариях: от обработки выражений и навигации по структурам данных до управления асинхронными задачами и обработки пользовательских запросов.

Ключевые моменты:

1. **Стек (LIFO)** особенно полезен для:
   - Управления историей и отмены действий
   - Проверки сбалансированности скобок
   - Вычисления выражений
   - Обработки рекурсивных вызовов
   - Обхода графов и деревьев в глубину (DFS)

2. **Очередь (FIFO)** особенно полезна для:
   - Обработки запросов в порядке их поступления
   - Управления задачами с равным приоритетом
   - Обхода графов и деревьев в ширину (BFS)
   - Буферизации данных между процессами

3. **Очередь с приоритетом** позволяет обрабатывать элементы не в порядке их добавления, а на основе приоритета, что критически важно для многих реальных приложений.

4. **Двусторонняя очередь (deque)** предоставляет гибкость, позволяя добавлять и удалять элементы с обоих концов, что делает её универсальной структурой данных.

В веб-разработке эти структуры данных и связанные с ними алгоритмы позволяют создавать эффективные и масштабируемые приложения, способные обрабатывать сложные запросы, управлять состоянием и обеспечивать хороший пользовательский опыт даже при больших нагрузках.

Понимание стеков, очередей и алгоритмов на их основе — фундаментальный навык для любого разработчика, который хочет создавать производительные и хорошо структурированные приложения.

---

В следующем разделе мы рассмотрим алгоритмы на связных списках, которые являются еще одной важной структурой данных, особенно полезной для эффективного управления памятью и для реализации других более сложных структур данных.
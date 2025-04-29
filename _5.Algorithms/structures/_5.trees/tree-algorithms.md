# Алгоритмы на деревьях

Деревья — это иерархические структуры данных, которые широко используются в программировании для представления иерархических отношений между объектами. В отличие от линейных структур данных, таких как массивы и связные списки, деревья позволяют эффективно организовывать и искать данные в иерархической форме. Деревья находят применение в самых разных областях: от файловых систем и баз данных до компиляторов и алгоритмов искусственного интеллекта.

## Основы деревьев

### Определение и терминология

**Дерево** — это связная структура данных, состоящая из узлов (вершин), соединенных направленными ребрами (связями) и не содержащая циклов. В дереве выделяется один особый узел, называемый **корнем**.

Основные понятия, связанные с деревьями:

- **Корень (Root)** — вершина, не имеющая родителей.
- **Узел (Node)** — элемент дерева, содержащий данные и ссылки на дочерние узлы.
- **Лист (Leaf)** — узел без дочерних элементов.
- **Ребро (Edge)** — связь между двумя узлами.
- **Родитель (Parent)** — узел, имеющий дочерние элементы.
- **Потомок (Child)** — узел, связанный с родительским.
- **Глубина узла (Depth)** — длина пути от корня до узла.
- **Высота дерева (Height)** — максимальная глубина любого узла в дереве.
- **Поддерево (Subtree)** — дерево, образованное узлом и всеми его потомками.
- **Уровень (Level)** — множество узлов на одинаковой глубине.

### Виды деревьев

1. **Бинарное дерево (Binary Tree)** — дерево, в котором каждый узел имеет не более двух потомков, обычно называемых левым и правым.

2. **Бинарное дерево поиска (Binary Search Tree, BST)** — бинарное дерево, для которого выполняется следующее свойство: для каждого узла все значения в его левом поддереве меньше значения в узле, а все значения в правом поддереве больше.

3. **Сбалансированное дерево (Balanced Tree)** — дерево, в котором глубины всех листьев отличаются не более чем на 1. Примеры: АВЛ-деревья, красно-черные деревья.

4. **Полное бинарное дерево (Complete Binary Tree)** — бинарное дерево, у которого заполнены все уровни, кроме, возможно, последнего, и на последнем уровне все узлы расположены слева направо без пропусков.

5. **Совершенное бинарное дерево (Perfect Binary Tree)** — бинарное дерево, в котором все внутренние узлы имеют двух потомков, и все листья находятся на одном уровне.

6. **Вырожденное дерево (Degenerate Tree)** — дерево, в котором каждый родительский узел имеет только одного потомка, фактически превращаясь в связный список.

7. **К-арное дерево (K-ary Tree)** — дерево, в котором каждый узел имеет не более K потомков.

8. **Префиксное дерево (Trie)** — дерево, используемое для хранения ассоциативного массива, где ключами обычно являются строки.

9. **Дерево отрезков (Segment Tree)** — дерево для хранения интервалов или отрезков с возможностью быстро находить интервалы, пересекающиеся с заданным.

10. **Дерево Фенвика (Fenwick Tree или Binary Indexed Tree)** — структура данных, позволяющая эффективно вычислять кумулятивные суммы в изменяемом массиве.

## Реализация бинарного дерева в Python

Начнем с базовой реализации бинарного дерева:

```python
class TreeNode:
    """
    Класс для представления узла бинарного дерева.
    """
    def __init__(self, value):
        self.value = value      # Значение узла
        self.left = None        # Ссылка на левого потомка
        self.right = None       # Ссылка на правого потомка
    
    def __str__(self):
        return str(self.value)

class BinaryTree:
    """
    Класс для представления бинарного дерева.
    """
    def __init__(self, root=None):
        self.root = root    # Корень дерева
    
    def is_empty(self):
        """Проверяет, пусто ли дерево."""
        return self.root is None
    
    def insert(self, value):
        """
        Вставляет новое значение в дерево.
        Для простоты вставляем в первое свободное место,
        используя обход в ширину.
        
        Args:
            value: Значение для вставки
        """
        if self.is_empty():
            self.root = TreeNode(value)
            return
        
        # Используем очередь для обхода в ширину
        queue = [self.root]
        
        while queue:
            node = queue.pop(0)
            
            # Если левый потомок отсутствует, вставляем туда
            if node.left is None:
                node.left = TreeNode(value)
                return
            else:
                queue.append(node.left)
            
            # Если правый потомок отсутствует, вставляем туда
            if node.right is None:
                node.right = TreeNode(value)
                return
            else:
                queue.append(node.right)
    
    def height(self, node=None):
        """
        Возвращает высоту дерева или поддерева.
        
        Args:
            node: Корень поддерева, для которого вычисляется высота
                 (по умолчанию - корень всего дерева)
            
        Returns:
            Высота дерева или поддерева
        """
        if node is None:
            node = self.root
        
        if node is None:
            return 0
        
        # Вычисляем высоту левого и правого поддеревьев
        left_height = self.height(node.left)
        right_height = self.height(node.right)
        
        # Возвращаем максимум из высот поддеревьев плюс 1 (для текущего узла)
        return max(left_height, right_height) + 1
    
    def size(self, node=None):
        """
        Возвращает количество узлов в дереве или поддереве.
        
        Args:
            node: Корень поддерева, для которого вычисляется размер
                 (по умолчанию - корень всего дерева)
            
        Returns:
            Количество узлов
        """
        if node is None:
            node = self.root
        
        if node is None:
            return 0
        
        # Размер дерева = 1 (текущий узел) + размер левого поддерева + размер правого поддерева
        return 1 + self.size(node.left) + self.size(node.right)
    
    def __str__(self):
        """
        Возвращает строковое представление дерева.
        Использует обход в ширину для вывода по уровням.
        """
        if self.is_empty():
            return "[]"
        
        result = []
        queue = [(self.root, 0)]  # (узел, уровень)
        current_level = 0
        level_nodes = []
        
        while queue:
            node, level = queue.pop(0)
            
            # Если перешли на новый уровень, печатаем узлы предыдущего
            if level > current_level:
                result.append(" ".join(map(str, level_nodes)))
                level_nodes = []
                current_level = level
            
            # Добавляем узел на текущий уровень
            level_nodes.append(node.value if node else "None")
            
            # Добавляем потомков в очередь
            if node:
                queue.append((node.left, level + 1))
                queue.append((node.right, level + 1))
        
        # Добавляем последний уровень
        if level_nodes:
            result.append(" ".join(map(str, level_nodes)))
        
        return "\n".join(result)

# Пример использования
tree = BinaryTree()
for value in [1, 2, 3, 4, 5, 6, 7]:
    tree.insert(value)

print("Дерево по уровням:")
print(tree)
print(f"Высота дерева: {tree.height()}")
print(f"Количество узлов: {tree.size()}")
```

## Алгоритмы обхода деревьев

Существует несколько способов обхода (обхода всех узлов) дерева:

### 1. Обход в глубину (Depth-First Search, DFS)

При обходе в глубину мы полностью исследуем одну ветвь дерева, прежде чем перейти к другой. Существует три варианта обхода в глубину:

#### 1.1. Прямой обход (Preorder Traversal): Корень -> Левое поддерево -> Правое поддерево

```python
def preorder_traversal(self, node=None, result=None):
    """
    Прямой обход дерева: Корень -> Левое поддерево -> Правое поддерево.
    
    Args:
        node: Текущий узел (по умолчанию - корень дерева)
        result: Список для хранения результатов
        
    Returns:
        Список значений узлов в порядке прямого обхода
    """
    if result is None:
        result = []
    
    if node is None:
        node = self.root
    
    if node is None:
        return result
    
    # Обрабатываем корень
    result.append(node.value)
    
    # Обрабатываем левое поддерево
    self.preorder_traversal(node.left, result)
    
    # Обрабатываем правое поддерево
    self.preorder_traversal(node.right, result)
    
    return result
```

#### 1.2. Симметричный обход (Inorder Traversal): Левое поддерево -> Корень -> Правое поддерево

```python
def inorder_traversal(self, node=None, result=None):
    """
    Симметричный обход дерева: Левое поддерево -> Корень -> Правое поддерево.
    
    Args:
        node: Текущий узел (по умолчанию - корень дерева)
        result: Список для хранения результатов
        
    Returns:
        Список значений узлов в порядке симметричного обхода
    """
    if result is None:
        result = []
    
    if node is None:
        node = self.root
    
    if node is None:
        return result
    
    # Обрабатываем левое поддерево
    self.inorder_traversal(node.left, result)
    
    # Обрабатываем корень
    result.append(node.value)
    
    # Обрабатываем правое поддерево
    self.inorder_traversal(node.right, result)
    
    return result
```

#### 1.3. Обратный обход (Postorder Traversal): Левое поддерево -> Правое поддерево -> Корень

```python
def postorder_traversal(self, node=None, result=None):
    """
    Обратный обход дерева: Левое поддерево -> Правое поддерево -> Корень.
    
    Args:
        node: Текущий узел (по умолчанию - корень дерева)
        result: Список для хранения результатов
        
    Returns:
        Список значений узлов в порядке обратного обхода
    """
    if result is None:
        result = []
    
    if node is None:
        node = self.root
    
    if node is None:
        return result
    
    # Обрабатываем левое поддерево
    self.postorder_traversal(node.left, result)
    
    # Обрабатываем правое поддерево
    self.postorder_traversal(node.right, result)
    
    # Обрабатываем корень
    result.append(node.value)
    
    return result
```

### 2. Обход в ширину (Breadth-First Search, BFS)

При обходе в ширину мы посещаем все узлы на текущем уровне, прежде чем перейти к узлам на следующем уровне.

```python
def level_order_traversal(self):
    """
    Обход дерева в ширину (по уровням): уровень за уровнем, слева направо.
    
    Returns:
        Список значений узлов в порядке уровневого обхода
    """
    if self.is_empty():
        return []
    
    result = []
    queue = [self.root]
    
    while queue:
        node = queue.pop(0)
        result.append(node.value)
        
        if node.left:
            queue.append(node.left)
        
        if node.right:
            queue.append(node.right)
    
    return result
```

### 3. Итеративные реализации обходов

Рекурсивные реализации обходов просты, но могут привести к переполнению стека для глубоких деревьев. Вот итеративные реализации:

#### 3.1. Итеративный прямой обход

```python
def iterative_preorder(self):
    """
    Итеративная реализация прямого обхода дерева.
    
    Returns:
        Список значений узлов в порядке прямого обхода
    """
    if self.is_empty():
        return []
    
    result = []
    stack = [self.root]
    
    while stack:
        node = stack.pop()
        result.append(node.value)
        
        # Правого потомка добавляем первым, чтобы левый был обработан первым
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)
    
    return result
```

#### 3.2. Итеративный симметричный обход

```python
def iterative_inorder(self):
    """
    Итеративная реализация симметричного обхода дерева.
    
    Returns:
        Список значений узлов в порядке симметричного обхода
    """
    if self.is_empty():
        return []
    
    result = []
    stack = []
    current = self.root
    
    while current or stack:
        # Доходим до самого левого узла
        while current:
            stack.append(current)
            current = current.left
        
        # Обрабатываем текущий узел
        current = stack.pop()
        result.append(current.value)
        
        # Переходим к правому поддереву
        current = current.right
    
    return result
```

#### 3.3. Итеративный обратный обход

```python
def iterative_postorder(self):
    """
    Итеративная реализация обратного обхода дерева.
    
    Returns:
        Список значений узлов в порядке обратного обхода
    """
    if self.is_empty():
        return []
    
    result = []
    stack = []
    current = self.root
    last_visited = None
    
    while current or stack:
        # Доходим до самого левого узла
        if current:
            stack.append(current)
            current = current.left
        else:
            # Получаем верхний элемент стека
            peek = stack[-1]
            
            # Если у него есть правый потомок и он еще не посещен
            if peek.right and peek.right != last_visited:
                current = peek.right
            else:
                # Обрабатываем текущий узел
                result.append(peek.value)
                last_visited = stack.pop()
    
    return result
```

Добавим эти методы к нашему классу `BinaryTree` и протестируем их:

```python
# Добавляем методы обхода к классу BinaryTree

# Пример использования
tree = BinaryTree()
for value in [1, 2, 3, 4, 5, 6, 7]:
    tree.insert(value)

print("Дерево по уровням:")
print(tree)

print("Прямой обход (Preorder):", tree.preorder_traversal())
print("Симметричный обход (Inorder):", tree.inorder_traversal())
print("Обратный обход (Postorder):", tree.postorder_traversal())
print("Обход в ширину (Level Order):", tree.level_order_traversal())

print("\nИтеративные реализации:")
print("Итеративный прямой обход:", tree.iterative_preorder())
print("Итеративный симметричный обход:", tree.iterative_inorder())
print("Итеративный обратный обход:", tree.iterative_postorder())
```

## Бинарное дерево поиска (Binary Search Tree, BST)

Бинарное дерево поиска — это бинарное дерево, которое удовлетворяет следующему свойству: для каждого узла все значения в его левом поддереве меньше значения в узле, а все значения в правом поддереве больше.

### Реализация бинарного дерева поиска

```python
class BSTNode:
    """
    Класс для представления узла бинарного дерева поиска.
    """
    def __init__(self, value):
        self.value = value      # Значение узла
        self.left = None        # Ссылка на левого потомка
        self.right = None       # Ссылка на правого потомка
    
    def __str__(self):
        return str(self.value)

class BinarySearchTree:
    """
    Класс для представления бинарного дерева поиска.
    """
    def __init__(self):
        self.root = None    # Корень дерева
    
    def is_empty(self):
        """Проверяет, пусто ли дерево."""
        return self.root is None
    
    def insert(self, value):
        """
        Вставляет новое значение в дерево поиска.
        
        Args:
            value: Значение для вставки
        """
        if self.is_empty():
            self.root = BSTNode(value)
            return
        
        self._insert_recursive(self.root, value)
    
    def _insert_recursive(self, node, value):
        """
        Рекурсивно вставляет значение в поддерево.
        
        Args:
            node: Корень поддерева
            value: Значение для вставки
        """
        # Если значение меньше текущего, идем влево
        if value < node.value:
            if node.left is None:
                node.left = BSTNode(value)
            else:
                self._insert_recursive(node.left, value)
        # Если значение больше или равно текущему, идем вправо
        else:
            if node.right is None:
                node.right = BSTNode(value)
            else:
                self._insert_recursive(node.right, value)
    
    def search(self, value):
        """
        Ищет значение в дереве поиска.
        
        Args:
            value: Искомое значение
            
        Returns:
            True, если значение найдено, иначе False
        """
        return self._search_recursive(self.root, value)
    
    def _search_recursive(self, node, value):
        """
        Рекурсивно ищет значение в поддереве.
        
        Args:
            node: Корень поддерева
            value: Искомое значение
            
        Returns:
            True, если значение найдено, иначе False
        """
        # Базовый случай: дошли до листа или нашли значение
        if node is None:
            return False
        
        if node.value == value:
            return True
        
        # Рекурсивный случай: ищем в соответствующем поддереве
        if value < node.value:
            return self._search_recursive(node.left, value)
        else:
            return self._search_recursive(node.right, value)
    
    def delete(self, value):
        """
        Удаляет значение из дерева поиска.
        
        Args:
            value: Значение для удаления
            
        Returns:
            True, если значение успешно удалено, иначе False
        """
        if self.is_empty():
            return False
        
        # Корень может измениться после удаления
        self.root, deleted = self._delete_recursive(self.root, value)
        return deleted
    
    def _delete_recursive(self, node, value):
        """
        Рекурсивно удаляет значение из поддерева.
        
        Args:
            node: Корень поддерева
            value: Значение для удаления
            
        Returns:
            (new_root, deleted): Новый корень поддерева и флаг успешного удаления
        """
        if node is None:
            return None, False
        
        deleted = False
        
        # Ищем узел для удаления
        if value < node.value:
            node.left, deleted = self._delete_recursive(node.left, value)
        elif value > node.value:
            node.right, deleted = self._delete_recursive(node.right, value)
        else:
            # Нашли узел для удаления
            
            # Случай 1: Узел без потомков (лист)
            if node.left is None and node.right is None:
                return None, True
            
            # Случай 2: Узел с одним потомком
            if node.left is None:
                return node.right, True
            if node.right is None:
                return node.left, True
            
            # Случай 3: Узел с двумя потомками
            # Находим наименьший узел в правом поддереве (или наибольший в левом)
            node.value = self._find_min_value(node.right)
            # Удаляем найденный наименьший узел из правого поддерева
            node.right, _ = self._delete_recursive(node.right, node.value)
            deleted = True
        
        return node, deleted
    
    def _find_min_value(self, node):
        """
        Находит наименьшее значение в поддереве.
        
        Args:
            node: Корень поддерева
            
        Returns:
            Наименьшее значение
        """
        current = node
        while current.left:
            current = current.left
        return current.value
    
    def find_min(self):
        """
        Находит наименьшее значение в дереве.
        
        Returns:
            Наименьшее значение или None, если дерево пусто
        """
        if self.is_empty():
            return None
        
        current = self.root
        while current.left:
            current = current.left
        return current.value
    
    def find_max(self):
        """
        Находит наибольшее значение в дереве.
        
        Returns:
            Наибольшее значение или None, если дерево пусто
        """
        if self.is_empty():
            return None
        
        current = self.root
        while current.right:
            current = current.right
        return current.value
    
    def inorder_traversal(self):
        """
        Симметричный обход дерева.
        
        Returns:
            Список значений узлов в порядке возрастания
        """
        result = []
        self._inorder_recursive(self.root, result)
        return result
    
    def _inorder_recursive(self, node, result):
        """
        Рекурсивно выполняет симметричный обход поддерева.
        
        Args:
            node: Корень поддерева
            result: Список для хранения результатов
        """
        if node:
            self._inorder_recursive(node.left, result)
            result.append(node.value)
            self._inorder_recursive(node.right, result)
    
    def is_valid_bst(self):
        """
        Проверяет, является ли дерево корректным бинарным деревом поиска.
        
        Returns:
            True, если дерево является BST, иначе False
        """
        def is_valid_subtree(node, min_val=float('-inf'), max_val=float('inf')):
            if node is None:
                return True
            
            if node.value <= min_val or node.value >= max_val:
                return False
            
            return (is_valid_subtree(node.left, min_val, node.value) and
                    is_valid_subtree(node.right, node.value, max_val))
        
        return is_valid_subtree(self.root)
    
    def print_tree(self):
        """
        Выводит дерево в удобочитаемом формате.
        """
        def print_recursive(node, prefix="", is_left=True):
            if node is None:
                return
            
            print(prefix + ("├── " if is_left else "└── ") + str(node.value))
            
            # Рекурсивно печатаем детей
            new_prefix = prefix + ("│   " if is_left else "    ")
            print_recursive(node.left, new_prefix, True)
            print_recursive(node.right, new_prefix, False)
        
        if self.is_empty():
            print("Пустое дерево")
        else:
            print_recursive(self.root, "")

# Пример использования
bst = BinarySearchTree()
values = [5, 3, 7, 2, 4, 6, 8]
for value in values:
    bst.insert(value)

print("Бинарное дерево поиска:")
bst.print_tree()

print("\nСимметричный обход (упорядоченный):", bst.inorder_traversal())
print(f"Минимальное значение: {bst.find_min()}")
print(f"Максимальное значение: {bst.find_max()}")

search_value = 4
print(f"Поиск значения {search_value}: {bst.search(search_value)}")

delete_value = 3
print(f"Удаление значения {delete_value}: {bst.delete(delete_value)}")
print("Дерево после удаления:")
bst.print_tree()

print(f"Является ли деревом поиска: {bst.is_valid_bst()}")
```

## Сбалансированные деревья

### 1. АВЛ-дерево (AVL Tree)

АВЛ-дерево — это самобалансирующееся бинарное дерево поиска, в котором разница высот левого и правого поддеревьев любого узла не превышает 1.

```python
class AVLNode:
    """
    Класс для представления узла АВЛ-дерева.
    """
    def __init__(self, value):
        self.value = value      # Значение узла
        self.left = None        # Ссылка на левого потомка
        self.right = None       # Ссылка на правого потомка
        self.height = 1         # Высота узла в дереве
    
    def __str__(self):
        return str(self.value)

class AVLTree:
    """
    Класс для представления АВЛ-дерева.
    """
    def __init__(self):
        self.root = None    # Корень дерева
    
    def height(self, node):
        """
        Возвращает высоту узла.
        
        Args:
            node: Узел
            
        Returns:
            Высота узла или 0, если узел None
        """
        if node is None:
            return 0
        return node.height
    
    def update_height(self, node):
        """
        Обновляет высоту узла.
        
        Args:
            node: Узел
        """
        if node:
            node.height = max(self.height(node.left), self.height(node.right)) + 1
    
    def balance_factor(self, node):
        """
        Вычисляет фактор баланса узла.
        
        Args:
            node: Узел
            
        Returns:
            Фактор баланса = высота правого поддерева - высота левого поддерева
        """
        if node is None:
            return 0
        return self.height(node.right) - self.height(node.left)
    
    def right_rotate(self, y):
        """
        Выполняет правый поворот вокруг узла y.
        
        Args:
            y: Узел для поворота
            
        Returns:
            Новый корень поддерева
        """
        # Узел х становится новым корнем
        x = y.left
        T2 = x.right
        
        # Выполняем поворот
        x.right = y
        y.left = T2
        
        # Обновляем высоты
        self.update_height(y)
        self.update_height(x)
        
        # Возвращаем новый корень
        return x
    
    def left_rotate(self, x):
        """
        Выполняет левый поворот вокруг узла x.
        
        Args:
            x: Узел для поворота
            
        Returns:
            Новый корень поддерева
        """
        # Узел y становится новым корнем
        y = x.right
        T2 = y.left
        
        # Выполняем поворот
        y.left = x
        x.right = T2
        
        # Обновляем высоты
        self.update_height(x)
        self.update_height(y)
        
        # Возвращаем новый корень
        return y
    
    def insert(self, value):
        """
        Вставляет новое значение в АВЛ-дерево.
        
        Args:
            value: Значение для вставки
        """
        self.root = self._insert_recursive(self.root, value)
    
    def _insert_recursive(self, node, value):
        """
        Рекурсивно вставляет значение в поддерево и балансирует его.
        
        Args:
            node: Корень поддерева
            value: Значение для вставки
            
        Returns:
            Новый корень поддерева
        """
        # Стандартная вставка в BST
        if node is None:
            return AVLNode(value)
        
        if value < node.value:
            node.left = self._insert_recursive(node.left, value)
        else:
            node.right = self._insert_recursive(node.right, value)
        
        # Обновляем высоту узла
        self.update_height(node)
        
        # Получаем фактор баланса
        balance = self.balance_factor(node)
        
        # Если узел несбалансирован, то есть 4 случая
        
        # Левый-Левый (LL) случай
        if balance < -1 and value < node.left.value:
            return self.right_rotate(node)
        
        # Правый-Правый (RR) случай
        if balance > 1 and value >= node.right.value:
            return self.left_rotate(node)
        
        # Левый-Правый (LR) случай
        if balance < -1 and value >= node.left.value:
            node.left = self.left_rotate(node.left)
            return self.right_rotate(node)
        
        # Правый-Левый (RL) случай
        if balance > 1 and value < node.right.value:
            node.right = self.right_rotate(node.right)
            return self.left_rotate(node)
        
        # Если узел сбалансирован
        return node
    
    def delete(self, value):
        """
        Удаляет значение из АВЛ-дерева.
        
        Args:
            value: Значение для удаления
        """
        if self.root:
            self.root = self._delete_recursive(self.root, value)
    
    def _delete_recursive(self, node, value):
        """
        Рекурсивно удаляет значение из поддерева и балансирует его.
        
        Args:
            node: Корень поддерева
            value: Значение для удаления
            
        Returns:
            Новый корень поддерева
        """
        # Стандартное удаление из BST
        if node is None:
            return node
        
        if value < node.value:
            node.left = self._delete_recursive(node.left, value)
        elif value > node.value:
            node.right = self._delete_recursive(node.right, value)
        else:
            # Нашли узел для удаления
            
            # Случай 1: Узел без потомков или с одним потомком
            if node.left is None:
                return node.right
            elif node.right is None:
                return node.left
            
            # Случай 2: Узел с двумя потомками
            # Находим наименьший узел в правом поддереве
            temp = self._find_min_node(node.right)
            node.value = temp.value
            # Удаляем наименьший узел из правого поддерева
            node.right = self._delete_recursive(node.right, temp.value)
        
        # Если после удаления дерево стало пустым
        if node is None:
            return node
        
        # Обновляем высоту узла
        self.update_height(node)
        
        # Получаем фактор баланса
        balance = self.balance_factor(node)
        
        # Если узел несбалансирован, то есть 4 случая
        
        # Левый-Левый (LL) случай
        if balance < -1 and self.balance_factor(node.left) <= 0:
            return self.right_rotate(node)
        
        # Левый-Правый (LR) случай
        if balance < -1 and self.balance_factor(node.left) > 0:
            node.left = self.left_rotate(node.left)
            return self.right_rotate(node)
        
        # Правый-Правый (RR) случай
        if balance > 1 and self.balance_factor(node.right) >= 0:
            return self.left_rotate(node)
        
        # Правый-Левый (RL) случай
        if balance > 1 and self.balance_factor(node.right) < 0:
            node.right = self.right_rotate(node.right)
            return self.left_rotate(node)
        
        # Если узел сбалансирован
        return node
    
    def _find_min_node(self, node):
        """
        Находит узел с наименьшим значением в поддереве.
        
        Args:
            node: Корень поддерева
            
        Returns:
            Узел с наименьшим значением
        """
        current = node
        while current.left:
            current = current.left
        return current
    
    def search(self, value):
        """
        Ищет значение в АВЛ-дереве.
        
        Args:
            value: Искомое значение
            
        Returns:
            True, если значение найдено, иначе False
        """
        return self._search_recursive(self.root, value)
    
    def _search_recursive(self, node, value):
        """
        Рекурсивно ищет значение в поддереве.
        
        Args:
            node: Корень поддерева
            value: Искомое значение
            
        Returns:
            True, если значение найдено, иначе False
        """
        if node is None:
            return False
        
        if node.value == value:
            return True
        
        if value < node.value:
            return self._search_recursive(node.left, value)
        else:
            return self._search_recursive(node.right, value)
    
    def inorder_traversal(self):
        """
        Симметричный обход АВЛ-дерева.
        
        Returns:
            Список значений узлов в порядке возрастания
        """
        result = []
        self._inorder_recursive(self.root, result)
        return result
    
    def _inorder_recursive(self, node, result):
        """
        Рекурсивно выполняет симметричный обход поддерева.
        
        Args:
            node: Корень поддерева
            result: Список для хранения результатов
        """
        if node:
            self._inorder_recursive(node.left, result)
            result.append(node.value)
            self._inorder_recursive(node.right, result)
    
    def print_tree(self):
        """
        Выводит дерево в удобочитаемом формате с высотами узлов.
        """
        def print_recursive(node, prefix="", is_left=True):
            if node is None:
                return
            
            print(prefix + ("├── " if is_left else "└── ") + f"{node.value} (h={node.height})")
            
            # Рекурсивно печатаем детей
            new_prefix = prefix + ("│   " if is_left else "    ")
            print_recursive(node.left, new_prefix, True)
            print_recursive(node.right, new_prefix, False)
        
        if self.root is None:
            print("Пустое дерево")
        else:
            print_recursive(self.root, "")

# Пример использования
avl = AVLTree()
values = [9, 5, 10, 0, 6, 11, -1, 1, 2]
for value in values:
    avl.insert(value)

print("АВЛ-дерево после вставки:")
avl.print_tree()

print("\nСимметричный обход (упорядоченный):", avl.inorder_traversal())

delete_value = 10
print(f"\nУдаление значения {delete_value}")
avl.delete(delete_value)
print("АВЛ-дерево после удаления:")
avl.print_tree()

search_value = 6
print(f"\nПоиск значения {search_value}: {avl.search(search_value)}")
```

### 2. Красно-черное дерево (Red-Black Tree)

Красно-черное дерево — это самобалансирующееся бинарное дерево поиска, в котором каждый узел имеет дополнительный бит для обозначения цвета узла (красный или черный). Эти цвета используются для обеспечения того, чтобы дерево оставалось приблизительно сбалансированным во время вставок и удалений.

Свойства красно-черного дерева:
1. Каждый узел либо красный, либо черный.
2. Корень всегда черный.
3. Если узел красный, то оба его потомка черные.
4. Каждый путь от корня до любого листа (null) содержит одинаковое количество черных узлов.

```python
class RBNode:
    """
    Класс для представления узла красно-черного дерева.
    """
    def __init__(self, value):
        self.value = value      # Значение узла
        self.left = None        # Ссылка на левого потомка
        self.right = None       # Ссылка на правого потомка
        self.parent = None      # Ссылка на родителя
        self.color = "RED"      # Цвет узла (RED или BLACK)
    
    def __str__(self):
        return f"{self.value} ({'R' if self.color == 'RED' else 'B'})"

class RedBlackTree:
    """
    Класс для представления красно-черного дерева.
    """
    def __init__(self):
        self.NIL = RBNode(None)  # Листовой узел (NIL)
        self.NIL.color = "BLACK"
        self.NIL.left = None
        self.NIL.right = None
        self.root = self.NIL     # Корень дерева
    
    def insert(self, value):
        """
        Вставляет новое значение в красно-черное дерево.
        
        Args:
            value: Значение для вставки
        """
        # Создаем новый узел
        new_node = RBNode(value)
        new_node.left = self.NIL
        new_node.right = self.NIL
        
        # Стандартная вставка в BST
        if self.root == self.NIL:
            self.root = new_node
            new_node.color = "BLACK"  # Корень всегда черный
            new_node.parent = None
            return
        
        # Ищем место для вставки
        current = self.root
        parent = None
        
        while current != self.NIL:
            parent = current
            if new_node.value < current.value:
                current = current.left
            else:
                current = current.right
        
        # Вставляем узел
        new_node.parent = parent
        if new_node.value < parent.value:
            parent.left = new_node
        else:
            parent.right = new_node
        
        # Восстанавливаем свойства красно-черного дерева
        self._fix_insert(new_node)
    
    def _fix_insert(self, node):
        """
        Восстанавливает свойства красно-черного дерева после вставки.
        
        Args:
            node: Вставленный узел
        """
        # Пока родитель узла красный
        while node != self.root and node.parent.color == "RED":
            # Если родитель является левым потомком своего родителя (деда node)
            if node.parent == node.parent.parent.left:
                uncle = node.parent.parent.right
                
                # Случай 1: Дядя красный, перекрашиваем
                if uncle.color == "RED":
                    node.parent.color = "BLACK"
                    uncle.color = "BLACK"
                    node.parent.parent.color = "RED"
                    node = node.parent.parent
                else:
                    # Случай 2: Дядя черный, узел является правым потомком
                    if node == node.parent.right:
                        node = node.parent
                        self._left_rotate(node)
                    
                    # Случай 3: Дядя черный, узел является левым потомком
                    node.parent.color = "BLACK"
                    node.parent.parent.color = "RED"
                    self._right_rotate(node.parent.parent)
            else:
                # Если родитель является правым потомком своего родителя (деда node)
                # Случаи симметричны предыдущим
                uncle = node.parent.parent.left
                
                if uncle.color == "RED":
                    node.parent.color = "BLACK"
                    uncle.color = "BLACK"
                    node.parent.parent.color = "RED"
                    node = node.parent.parent
                else:
                    if node == node.parent.left:
                        node = node.parent
                        self._right_rotate(node)
                    
                    node.parent.color = "BLACK"
                    node.parent.parent.color = "RED"
                    self._left_rotate(node.parent.parent)
        
        # Корень всегда черный
        self.root.color = "BLACK"
    
    def _left_rotate(self, x):
        """
        Выполняет левый поворот вокруг узла x.
        
        Args:
            x: Узел для поворота
        """
        y = x.right
        
        # Связываем левое поддерево y с x
        x.right = y.left
        if y.left != self.NIL:
            y.left.parent = x
        
        # Связываем родителя x с y
        y.parent = x.parent
        if x.parent is None:
            self.root = y
        elif x == x.parent.left:
            x.parent.left = y
        else:
            x.parent.right = y
        
        # Связываем x и y
        y.left = x
        x.parent = y
    
    def _right_rotate(self, y):
        """
        Выполняет правый поворот вокруг узла y.
        
        Args:
            y: Узел для поворота
        """
        x = y.left
        
        # Связываем правое поддерево x с y
        y.left = x.right
        if x.right != self.NIL:
            x.right.parent = y
        
        # Связываем родителя y с x
        x.parent = y.parent
        if y.parent is None:
            self.root = x
        elif y == y.parent.left:
            y.parent.left = x
        else:
            y.parent.right = x
        
        # Связываем y и x
        x.right = y
        y.parent = x
    
    def search(self, value):
        """
        Ищет значение в красно-черном дереве.
        
        Args:
            value: Искомое значение
            
        Returns:
            True, если значение найдено, иначе False
        """
        current = self.root
        
        while current != self.NIL:
            if current.value == value:
                return True
            
            if value < current.value:
                current = current.left
            else:
                current = current.right
        
        return False
    
    def inorder_traversal(self):
        """
        Симметричный обход красно-черного дерева.
        
        Returns:
            Список значений узлов в порядке возрастания
        """
        result = []
        self._inorder_recursive(self.root, result)
        return result
    
    def _inorder_recursive(self, node, result):
        """
        Рекурсивно выполняет симметричный обход поддерева.
        
        Args:
            node: Корень поддерева
            result: Список для хранения результатов
        """
        if node != self.NIL:
            self._inorder_recursive(node.left, result)
            result.append(node.value)
            self._inorder_recursive(node.right, result)
    
    def print_tree(self):
        """
        Выводит дерево в удобочитаемом формате с цветами узлов.
        """
        def print_recursive(node, prefix="", is_left=True):
            if node == self.NIL:
                return
            
            print(prefix + ("├── " if is_left else "└── ") + f"{node.value} ({'R' if node.color == 'RED' else 'B'})")
            
            # Рекурсивно печатаем детей
            new_prefix = prefix + ("│   " if is_left else "    ")
            print_recursive(node.left, new_prefix, True)
            print_recursive(node.right, new_prefix, False)
        
        if self.root == self.NIL:
            print("Пустое дерево")
        else:
            print_recursive(self.root, "")

# Пример использования
rb = RedBlackTree()
values = [7, 3, 18, 10, 22, 8, 11, 26, 2, 6, 13]
for value in values:
    rb.insert(value)

print("Красно-черное дерево после вставки:")
rb.print_tree()

print("\nСимметричный обход (упорядоченный):", rb.inorder_traversal())

search_value = 13
print(f"\nПоиск значения {search_value}: {rb.search(search_value)}")
```

## Алгоритмы и задачи на деревьях

### 1. Проверка, является ли бинарное дерево бинарным деревом поиска

```python
def is_binary_search_tree(root, min_val=float('-inf'), max_val=float('inf')):
    """
    Проверяет, является ли дерево бинарным деревом поиска.
    
    Args:
        root: Корень дерева
        min_val: Минимальное допустимое значение
        max_val: Максимальное допустимое значение
        
    Returns:
        True, если дерево является BST, иначе False
    """
    # Пустое дерево является BST
    if root is None:
        return True
    
    # Проверяем, что текущий узел находится в допустимом диапазоне
    if root.value <= min_val or root.value >= max_val:
        return False
    
    # Рекурсивно проверяем левое и правое поддеревья
    return (is_binary_search_tree(root.left, min_val, root.value) and
            is_binary_search_tree(root.right, root.value, max_val))
```

### 2. Нахождение наименьшего общего предка двух узлов в бинарном дереве

```python
def lowest_common_ancestor(root, node1, node2):
    """
    Находит наименьший общий предок двух узлов в бинарном дереве.
    
    Args:
        root: Корень дерева
        node1: Первый узел
        node2: Второй узел
        
    Returns:
        Наименьший общий предок или None, если узлы не найдены
    """
    # Базовый случай
    if root is None:
        return None
    
    # Если один из узлов совпадает с корнем, корень и есть LCA
    if root.value == node1 or root.value == node2:
        return root
    
    # Ищем LCA в левом и правом поддеревьях
    left_lca = lowest_common_ancestor(root.left, node1, node2)
    right_lca = lowest_common_ancestor(root.right, node1, node2)
    
    # Если оба вызова вернули не None, значит узлы находятся 
    # в разных поддеревьях, и текущий узел - LCA
    if left_lca and right_lca:
        return root
    
    # Иначе возвращаем тот результат, который не None
    return left_lca if left_lca else right_lca
```

### 3. Поиск максимального пути в бинарном дереве

```python
def max_path_sum(root):
    """
    Находит максимальную сумму пути в бинарном дереве.
    
    Args:
        root: Корень дерева
        
    Returns:
        Максимальная сумма пути
    """
    # Используем список для хранения глобального максимума
    max_sum = [float('-inf')]
    
    def max_path_sum_recursive(node):
        if node is None:
            return 0
        
        # Находим максимальную сумму в левом и правом поддеревьях
        left_sum = max(0, max_path_sum_recursive(node.left))
        right_sum = max(0, max_path_sum_recursive(node.right))
        
        # Обновляем глобальный максимум (путь, проходящий через текущий узел)
        max_sum[0] = max(max_sum[0], left_sum + right_sum + node.value)
        
        # Возвращаем максимальную сумму пути, начинающегося с текущего узла
        return max(left_sum, right_sum) + node.value
    
    max_path_sum_recursive(root)
    return max_sum[0]
```

### 4. Построение дерева из его обходов

```python
def build_tree_from_inorder_and_preorder(inorder, preorder):
    """
    Строит бинарное дерево из его симметричного и прямого обходов.
    
    Args:
        inorder: Список значений узлов в порядке симметричного обхода
        preorder: Список значений узлов в порядке прямого обхода
        
    Returns:
        Корень построенного дерева
    """
    if not inorder or not preorder:
        return None
    
    # Создаем словарь для быстрого поиска индексов в симметричном обходе
    inorder_map = {value: i for i, value in enumerate(inorder)}
    
    def build_tree_recursive(inorder_start, inorder_end, preorder_start, preorder_end):
        if inorder_start > inorder_end or preorder_start > preorder_end:
            return None
        
        # Корень - первый элемент в прямом обходе
        root_value = preorder[preorder_start]
        root = TreeNode(root_value)
        
        # Находим позицию корня в симметричном обходе
        inorder_index = inorder_map[root_value]
        
        # Вычисляем размер левого поддерева
        left_size = inorder_index - inorder_start
        
        # Рекурсивно строим левое и правое поддеревья
        root.left = build_tree_recursive(
            inorder_start, inorder_index - 1,
            preorder_start + 1, preorder_start + left_size
        )
        
        root.right = build_tree_recursive(
            inorder_index + 1, inorder_end,
            preorder_start + left_size + 1, preorder_end
        )
        
        return root
    
    return build_tree_recursive(0, len(inorder) - 1, 0, len(preorder) - 1)
```

### 5. Сериализация и десериализация бинарного дерева

```python
def serialize(root):
    """
    Сериализует бинарное дерево в строку.
    
    Args:
        root: Корень дерева
        
    Returns:
        Строковое представление дерева
    """
    if root is None:
        return "None,"
    
    # Используем прямой обход для сериализации
    serialized = str(root.value) + ","
    serialized += serialize(root.left)
    serialized += serialize(root.right)
    
    return serialized

def deserialize(data):
    """
    Десериализует строку в бинарное дерево.
    
    Args:
        data: Строковое представление дерева
        
    Returns:
        Корень десериализованного дерева
    """
    def deserialize_helper(nodes):
        if nodes[0] == "None":
            nodes.pop(0)
            return None
        
        root = TreeNode(int(nodes[0]))
        nodes.pop(0)
        
        root.left = deserialize_helper(nodes)
        root.right = deserialize_helper(nodes)
        
        return root
    
    nodes = data.strip(',').split(',')
    return deserialize_helper(nodes)
```

### 6. Зигзагообразный обход дерева

```python
def zigzag_level_order(root):
    """
    Выполняет зигзагообразный обход дерева по уровням.
    
    Args:
        root: Корень дерева
        
    Returns:
        Список списков значений узлов по уровням, с чередованием направления
    """
    if root is None:
        return []
    
    result = []
    queue = [root]
    left_to_right = True
    
    while queue:
        level_size = len(queue)
        level = []
        
        for _ in range(level_size):
            node = queue.pop(0)
            level.append(node.value)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        # Если направление справа налево, переворачиваем уровень
        if not left_to_right:
            level.reverse()
        
        result.append(level)
        left_to_right = not left_to_right
    
    return result
```

### 7. Проверка симметричности дерева

```python
def is_symmetric(root):
    """
    Проверяет, является ли дерево симметричным.
    
    Args:
        root: Корень дерева
        
    Returns:
        True, если дерево симметрично, иначе False
    """
    if root is None:
        return True
    
    return is_mirror(root.left, root.right)

def is_mirror(left, right):
    """
    Проверяет, являются ли два поддерева зеркальным отображением друг друга.
    
    Args:
        left: Корень левого поддерева
        right: Корень правого поддерева
        
    Returns:
        True, если поддеревья являются зеркальным отображением, иначе False
    """
    # Если оба поддерева пусты, они зеркальны
    if left is None and right is None:
        return True
    
    # Если только одно поддерево пусто, они не зеркальны
    if left is None or right is None:
        return False
    
    # Проверяем значения корней и рекурсивно проверяем поддеревья
    return (left.value == right.value and
            is_mirror(left.left, right.right) and
            is_mirror(left.right, right.left))
```

## Практические применения деревьев

### 1. Реализация префиксного дерева (Trie)

Префиксное дерево, или бор, — это древовидная структура данных, используемая для хранения набора строк, обычно используемая для эффективного поиска ключей в большом наборе строк.

```python
class TrieNode:
    """
    Класс для представления узла префиксного дерева.
    """
    def __init__(self):
        self.children = {}  # Словарь дочерних узлов {символ: узел}
        self.is_end_of_word = False  # Флаг конца слова

class Trie:
    """
    Класс для представления префиксного дерева (бора).
    """
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word):
        """
        Вставляет слово в префиксное дерево.
        
        Args:
            word: Слово для вставки
        """
        current = self.root
        
        for char in word:
            if char not in current.children:
                current.children[char] = TrieNode()
            current = current.children[char]
        
        current.is_end_of_word = True
    
    def search(self, word):
        """
        Ищет слово в префиксном дереве.
        
        Args:
            word: Слово для поиска
            
        Returns:
            True, если слово найдено, иначе False
        """
        current = self.root
        
        for char in word:
            if char not in current.children:
                return False
            current = current.children[char]
        
        return current.is_end_of_word
    
    def starts_with(self, prefix):
        """
        Проверяет, есть ли в дереве слова, начинающиеся с заданного префикса.
        
        Args:
            prefix: Префикс для проверки
            
        Returns:
            True, если есть слова с таким префиксом, иначе False
        """
        current = self.root
        
        for char in prefix:
            if char not in current.children:
                return False
            current = current.children[char]
        
        return True
    
    def get_words_with_prefix(self, prefix):
        """
        Возвращает все слова, начинающиеся с заданного префикса.
        
        Args:
            prefix: Префикс для поиска
            
        Returns:
            Список слов с заданным префиксом
        """
        result = []
        current = self.root
        
        # Достигаем конца префикса
        for char in prefix:
            if char not in current.children:
                return result
            current = current.children[char]
        
        # Собираем все слова с этим префиксом
        self._collect_words(current, prefix, result)
        
        return result
    
    def _collect_words(self, node, prefix, result):
        """
        Рекурсивно собирает все слова, начиная с данного узла.
        
        Args:
            node: Текущий узел
            prefix: Префикс, соответствующий пути до текущего узла
            result: Список для хранения результатов
        """
        if node.is_end_of_word:
            result.append(prefix)
        
        for char, child in node.children.items():
            self._collect_words(child, prefix + char, result)
    
    def delete(self, word):
        """
        Удаляет слово из префиксного дерева.
        
        Args:
            word: Слово для удаления
            
        Returns:
            True, если слово успешно удалено, иначе False
        """
        return self._delete_recursive(self.root, word, 0)
    
    def _delete_recursive(self, node, word, depth):
        """
        Рекурсивно удаляет слово из префиксного дерева.
        
        Args:
            node: Текущий узел
            word: Слово для удаления
            depth: Текущая глубина в слове
            
        Returns:
            True, если узел может быть удален, иначе False
        """
        # Если достигли конца слова
        if depth == len(word):
            # Если слово не существует в дереве
            if not node.is_end_of_word:
                return False
            
            # Сбрасываем флаг конца слова
            node.is_end_of_word = False
            
            # Если у узла нет дочерних элементов, его можно удалить
            return len(node.children) == 0
        
        char = word[depth]
        
        # Если следующий символ не существует в дереве
        if char not in node.children:
            return False
        
        # Рекурсивно проверяем, можно ли удалить дочерний узел
        should_delete_child = self._delete_recursive(node.children[char], word, depth + 1)
        
        # Если дочерний узел можно удалить
        if should_delete_child:
            del node.children[char]
            # Если текущий узел не помечен как конец слова и не имеет других детей
            return not node.is_end_of_word and len(node.children) == 0
        
        return False

# Пример использования
trie = Trie()
words = ["apple", "app", "apricot", "banana", "bat", "batman"]
for word in words:
    trie.insert(word)

print(f"Поиск 'apple': {trie.search('apple')}")
print(f"Поиск 'app': {trie.search('app')}")
print(f"Поиск 'orange': {trie.search('orange')}")

print(f"Начинается с 'ap': {trie.starts_with('ap')}")
print(f"Начинается с 'ba': {trie.starts_with('ba')}")
print(f"Начинается с 'c': {trie.starts_with('c')}")

print(f"Слова с префиксом 'ap': {trie.get_words_with_prefix('ap')}")
print(f"Слова с префиксом 'ba': {trie.get_words_with_prefix('ba')}")

trie.delete("app")
print(f"После удаления 'app':")
print(f"Поиск 'app': {trie.search('app')}")
print(f"Поиск 'apple': {trie.search('apple')}")
```

### 2. Дерево отрезков (Segment Tree)

Дерево отрезков — это структура данных, позволяющая выполнять различные операции над интервалами, такие как поиск минимума, максимума или суммы на заданном диапазоне.

```python
class SegmentTree:
    """
    Класс для представления дерева отрезков.
    """
    def __init__(self, arr):
        """
        Инициализирует дерево отрезков.
        
        Args:
            arr: Исходный массив
        """
        self.n = len(arr)
        
        # Размер дерева (4 * n достаточно для любого массива)
        self.tree = [0] * (4 * self.n)
        
        if self.n > 0:
            self._build(arr, 0, 0, self.n - 1)
    
    def _build(self, arr, node, start, end):
        """
        Строит дерево отрезков рекурсивно.
        
        Args:
            arr: Исходный массив
            node: Индекс текущего узла в дереве
            start: Начало интервала
            end: Конец интервала
        """
        # Если интервал состоит из одного элемента
        if start == end:
            self.tree[node] = arr[start]
            return
        
        # Находим середину интервала
        mid = (start + end) // 2
        
        # Рекурсивно строим левое и правое поддеревья
        self._build(arr, 2 * node + 1, start, mid)
        self._build(arr, 2 * node + 2, mid + 1, end)
        
        # Вычисляем значение для текущего узла на основе его детей
        self.tree[node] = self.tree[2 * node + 1] + self.tree[2 * node + 2]
    
    def query(self, left, right):
        """
        Выполняет запрос на диапазоне [left, right].
        
        Args:
            left: Левая граница диапазона
            right: Правая граница диапазона
            
        Returns:
            Сумма элементов на заданном диапазоне
        """
        if left < 0 or right >= self.n or left > right:
            return 0
        
        return self._query(0, 0, self.n - 1, left, right)
    
    def _query(self, node, start, end, left, right):
        """
        Рекурсивно выполняет запрос на диапазоне [left, right].
        
        Args:
            node: Индекс текущего узла в дереве
            start: Начало интервала, соответствующего текущему узлу
            end: Конец интервала, соответствующего текущему узлу
            left: Левая граница диапазона запроса
            right: Правая граница диапазона запроса
            
        Returns:
            Сумма элементов на заданном диапазоне
        """
        # Если интервал текущего узла полностью находится вне диапазона запроса
        if left > end or right < start:
            return 0
        
        # Если интервал текущего узла полностью находится внутри диапазона запроса
        if left <= start and end <= right:
            return self.tree[node]
        
        # Если интервал текущего узла частично находится внутри диапазона запроса
        mid = (start + end) // 2
        
        # Вычисляем результат для левого и правого поддеревьев
        return (self._query(2 * node + 1, start, mid, left, right) +
                self._query(2 * node + 2, mid + 1, end, left, right))
    
    def update(self, index, value):
        """
        Обновляет значение элемента в массиве.
        
        Args:
            index: Индекс элемента для обновления
            value: Новое значение
        """
        if index < 0 or index >= self.n:
            return
        
        self._update(0, 0, self.n - 1, index, value)
    
    def _update(self, node, start, end, index, value):
        """
        Рекурсивно обновляет дерево отрезков.
        
        Args:
            node: Индекс текущего узла в дереве
            start: Начало интервала, соответствующего текущему узлу
            end: Конец интервала, соответствующего текущему узлу
            index: Индекс элемента для обновления
            value: Новое значение
        """
        # Если индекс находится вне текущего интервала
        if index < start or index > end:
            return
        
        # Если интервал состоит из одного элемента (листовой узел)
        if start == end:
            self.tree[node] = value
            return
        
        # Находим середину интервала
        mid = (start + end) // 2
        
        # Рекурсивно обновляем соответствующее поддерево
        self._update(2 * node + 1, start, mid, index, value)
        self._update(2 * node + 2, mid + 1, end, index, value)
        
        # Обновляем значение текущего узла
        self.tree[node] = self.tree[2 * node + 1] + self.tree[2 * node + 2]

# Пример использования
arr = [1, 3, 5, 7, 9, 11]
segment_tree = SegmentTree(arr)

print(f"Сумма на диапазоне [1, 3]: {segment_tree.query(1, 3)}")
print(f"Сумма на диапазоне [0, 5]: {segment_tree.query(0, 5)}")

# Обновляем значение элемента
segment_tree.update(2, 10)

print(f"После обновления arr[2] = 10:")
print(f"Сумма на диапазоне [1, 3]: {segment_tree.query(1, 3)}")
print(f"Сумма на диапазоне [0, 5]: {segment_tree.query(0, 5)}")
```

### 3. Дерево Фенвика (Binary Indexed Tree)

Дерево Фенвика, или бинарное индексированное дерево, — это структура данных, позволяющая эффективно вычислять кумулятивные суммы и обновлять элементы в массиве.

```python
class FenwickTree:
    """
    Класс для представления дерева Фенвика (бинарного индексированного дерева).
    """
    def __init__(self, arr):
        """
        Инициализирует дерево Фенвика.
        
        Args:
            arr: Исходный массив
        """
        self.n = len(arr)
        self.bit = [0] * (self.n + 1)  # 1-индексация для упрощения операций
        
        # Заполняем дерево Фенвика
        for i in range(self.n):
            self.update(i, arr[i])
    
    def update(self, index, value):
        """
        Обновляет элемент в массиве.
        
        Args:
            index: Индекс элемента для обновления (0-индексация)
            value: Значение для добавления
        """
        # Преобразуем к 1-индексации
        index += 1
        
        # Обновляем все узлы, включающие этот индекс
        while index <= self.n:
            self.bit[index] += value
            # Переходим к следующему узлу, содержащему этот индекс
            index += index & -index  # Операция добавления наименее значимого единичного бита
    
    def prefix_sum(self, index):
        """
        Вычисляет сумму элементов от 0 до index.
        
        Args:
            index: Индекс элемента (0-индексация)
            
        Returns:
            Сумма элементов от 0 до index
        """
        # Преобразуем к 1-индексации
        index += 1
        
        # Суммируем все узлы, включающие элементы до этого индекса
        result = 0
        while index > 0:
            result += self.bit[index]
            # Переходим к следующему узлу, не содержащему этот индекс
            index -= index & -index  # Операция удаления наименее значимого единичного бита
        
        return result
    
    def range_sum(self, left, right):
        """
        Вычисляет сумму элементов на диапазоне [left, right].
        
        Args:
            left: Левая граница диапазона (0-индексация)
            right: Правая граница диапазона (0-индексация)
            
        Returns:
            Сумма элементов на заданном диапазоне
        """
        return self.prefix_sum(right) - (self.prefix_sum(left - 1) if left > 0 else 0)

# Пример использования
arr = [1, 3, 5, 7, 9, 11]
fenwick_tree = FenwickTree(arr)

print(f"Сумма на диапазоне [1, 3]: {fenwick_tree.range_sum(1, 3)}")
print(f"Сумма на диапазоне [0, 5]: {fenwick_tree.range_sum(0, 5)}")

# Обновляем значение элемента
fenwick_tree.update(2, 5)  # Добавляем 5 к arr[2]

print(f"После добавления 5 к arr[2]:")
print(f"Сумма на диапазоне [1, 3]: {fenwick_tree.range_sum(1, 3)}")
print(f"Сумма на диапазоне [0, 5]: {fenwick_tree.range_sum(0, 5)}")
```

### 4. Построение выражения из обратной польской записи

```python
def build_expression_tree(tokens):
    """
    Строит дерево выражения из обратной польской записи (постфиксной).
    
    Args:
        tokens: Список токенов в обратной польской записи
        
    Returns:
        Корень дерева выражения
    """
    class ExprNode:
        def __init__(self, value):
            self.value = value
            self.left = None
            self.right = None
        
        def __str__(self):
            return str(self.value)
    
    stack = []
    
    for token in tokens:
        # Если токен - оператор
        if token in "+-*/":
            # Операторы имеют два операнда
            if len(stack) < 2:
                raise ValueError("Некорректное выражение")
            
            # Создаем узел для оператора
            node = ExprNode(token)
            
            # Извлекаем два операнда (в обратном порядке)
            node.right = stack.pop()
            node.left = stack.pop()
            
            # Добавляем новый узел в стек
            stack.append(node)
        else:
            # Если токен - операнд, создаем для него узел
            stack.append(ExprNode(token))
    
    # В стеке должен остаться ровно один узел - корень дерева
    if len(stack) != 1:
        raise ValueError("Некорректное выражение")
    
    return stack[0]

def print_expression_tree(root, prefix="", is_left=True):
    """
    Выводит дерево выражения в удобочитаемом формате.
    
    Args:
        root: Корень дерева
        prefix: Строка-префикс для отступа
        is_left: Флаг, является ли узел левым потомком
    """
    if root is None:
        return
    
    print(prefix + ("├── " if is_left else "└── ") + str(root.value))
    
    # Рекурсивно печатаем детей
    new_prefix = prefix + ("│   " if is_left else "    ")
    print_expression_tree(root.left, new_prefix, True)
    print_expression_tree(root.right, new_prefix, False)

def evaluate_expression_tree(root):
    """
    Вычисляет значение выражения по дереву выражения.
    
    Args:
        root: Корень дерева выражения
        
    Returns:
        Результат вычисления выражения
    """
    if root is None:
        return 0
    
    # Если узел - лист (операнд)
    if root.left is None and root.right is None:
        return float(root.value)
    
    # Вычисляем значения левого и правого поддеревьев
    left_val = evaluate_expression_tree(root.left)
    right_val = evaluate_expression_tree(root.right)
    
    # Выполняем операцию
    if root.value == '+':
        return left_val + right_val
    elif root.value == '-':
        return left_val - right_val
    elif root.value == '*':
        return left_val * right_val
    elif root.value == '/':
        if right_val == 0:
            raise ValueError("Деление на ноль")
        return left_val / right_val
    
    raise ValueError(f"Неизвестный оператор: {root.value}")

# Пример использования
tokens = ["3", "4", "+", "2", "*", "7", "/"]  # (3 + 4) * 2 / 7
expr_tree = build_expression_tree(tokens)

print("Дерево выражения:")
print_expression_tree(expr_tree)

result = evaluate_expression_tree(expr_tree)
print(f"Результат вычисления: {result}")
```

## Преимущества и недостатки деревьев

### Преимущества:

1. **Иерархическая структура**: Деревья естественно представляют иерархические отношения между данными.

2. **Эффективный поиск**: Сбалансированные деревья поиска обеспечивают логарифмическое время поиска, вставки и удаления, что значительно быстрее, чем линейный поиск в массивах или связных списках.

3. **Упорядоченность**: Бинарные деревья поиска автоматически поддерживают элементы в упорядоченном состоянии.

4. **Гибкость**: Существует множество типов деревьев, каждое оптимизировано для определенного вида операций.

5. **Эффективное использование памяти**: Деревья могут динамически расти и сжиматься в зависимости от потребностей.

### Недостатки:

1. **Сложность реализации**: Деревья, особенно балансирующиеся, могут быть сложны в реализации и отладке.

2. **Дополнительная память**: Для хранения указателей требуется дополнительная память по сравнению с массивами.

3. **Несбалансированность**: Несбалансированные деревья могут вырождаться в связные списки, что приводит к ухудшению производительности.

4. **Кэш-локальность**: Элементы дерева могут быть разбросаны по памяти, что может приводить к большему количеству кэш-промахов по сравнению с массивами.

## Заключение

Деревья являются мощными и гибкими структурами данных, которые находят применение в самых разных областях программирования. Они особенно полезны, когда требуется эффективное хранение и поиск данных в иерархической форме.

В этом разделе мы рассмотрели различные типы деревьев, их реализации, основные операции и алгоритмы. Мы также изучили практическое применение деревьев для решения различных задач, от построения выражений до эффективного поиска и хранения строк.

Понимание деревьев и алгоритмов на их основе является фундаментальным навыком для любого программиста, который позволяет более эффективно решать широкий спектр задач и разрабатывать более производительные программы.

---

В следующем разделе мы рассмотрим алгоритмы на графах — более общей структуре данных, которая включает деревья как частный случай и позволяет моделировать еще более сложные отношения между объектами.
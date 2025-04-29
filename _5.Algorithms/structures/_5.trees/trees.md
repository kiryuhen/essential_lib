**Деревья**

## 1. Описание
Дерево — иерархическая структура данных, состоящая из узлов, где каждый узел содержит значение и ссылки на дочерние узлы. Особые понятия:
- **Корень (root)**: верхний узел.
- **Ребёнок (child)** и **родитель (parent)**: связь между узлами.
- **Лист (leaf)**: узел без детей.
- **Высота (height)**: наибольшая длина пути от узла до листа.
- **Глубина (depth)**: длина пути от корня до узла.

### Основные типы деревьев
- **Бинарное дерево (Binary Tree)**: каждый узел имеет не более двух детей.
- **Бинарное дерево поиска (BST)**: для каждого узла все значения в левом поддереве ≤, в правом ≥.
- **Сбалансированные деревья**:
  - **AVL-деревья**: балансировка по высоте, разность высот поддеревьев ≤ 1.
  - **Красно‑чёрные деревья**: каждая вершина имеет цвет, гарантируется логарифмическая высота.

## 2. Плюсы и минусы
**Плюсы:**
- Динамическая структура, легко изменяемая.
- Быстрые операции поиска, вставки и удаления в сбалансированных деревьях: O(log n).
- Естественная иерархическая модель данных.

**Минусы:**
- Несбалансированное BST может выродиться в список: худшие O(n).
- Сложность реализации балансировки.

## 3. Основные операции и их сложности (BST)
| Операция       | Сложность     |
|----------------|---------------|
| Поиск           | O(h)          |
| Вставка         | O(h)          |
| Удаление        | O(h)          |
| Поиск min/max   | O(h)          |

где h — высота дерева.

## 4. Алгоритмы
### 4.1 Обходы
- **Прямой (pre-order)**: узел, левое поддерево, правое.
- **Центральный (in-order)**: левое, узел, правое.
- **Обратный (post-order)**: левое, правое, узел.
- **BFS (обход в ширину)**: уровневый обход.

### 4.2 Операции в BST
- **Поиск**, **вставка**, **удаление** (случаи: удаление листа, узла с одним/двумя детьми).

### 4.3 Балансировка
- **Повороты** для AVL:
  - Левый поворот
  - Правый поворот
- **Правила окраски и повороты** для красно‑чёрных деревьев.

### 4.4 Другие задачи
- **Нахождение наименьшего общего предка (LCA)**: два указателя или хранение путей.

## 5. Псевдокод ключевых операций
```text
// In-order обход
Функция inorder(node):
    если node == null: вернуть
    inorder(node.left)
    обработать node.value
    inorder(node.right)

// Левый поворот (rotateLeft)
Функция rotateLeft(z):
    y = z.right
    T2 = y.left
    y.left = z
    z.right = T2
    обновить высоты z и y
    вернуть y
```

## 6. Реализация на Python
```python
class TreeNode:
    """Узел для бинарного дерева поиска"""
    def __init__(self, key):
        self.key = key
        self.left = None
        self.right = None

class BinarySearchTree:
    """Бинарное дерево поиска без балансировки"""
    def __init__(self):
        self.root = None

    def search(self, key):
        return self._search(self.root, key)

    def _search(self, node, key):
        if node is None or node.key == key:
            return node
        if key < node.key:
            return self._search(node.left, key)
        else:
            return self._search(node.right, key)

    def insert(self, key):
        self.root = self._insert(self.root, key)

    def _insert(self, node, key):
        if node is None:
            return TreeNode(key)
        if key < node.key:
            node.left = self._insert(node.left, key)
        else:
            node.right = self._insert(node.right, key)
        return node

    def delete(self, key):
        self.root = self._delete(self.root, key)

    def _delete(self, node, key):
        if node is None:
            return node
        if key < node.key:
            node.left = self._delete(node.left, key)
        elif key > node.key:
            node.right = self._delete(node.right, key)
        else:
            # удаляемый узел
            if node.left is None:
                return node.right
            if node.right is None:
                return node.left
            # два ребёнка: найти преемника
            succ = self._min_value_node(node.right)
            node.key = succ.key
            node.right = self._delete(node.right, succ.key)
        return node

    def _min_value_node(self, node):
        current = node
        while current.left:
            current = current.left
        return current

    def inorder(self, visit):
        self._inorder(self.root, visit)

    def _inorder(self, node, visit):
        if node:
            self._inorder(node.left, visit)
            visit(node.key)
            self._inorder(node.right, visit)
```


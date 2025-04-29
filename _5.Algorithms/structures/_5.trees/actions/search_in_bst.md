**Поиск в BST (Search in Binary Search Tree)**

## 1. Описание
Поиск элемента по ключу в бинарном дереве поиска, используя свойства упорядоченности.

## 2. Сложность
- **Время:** O(h)
- **Память:** O(h) для рекурсивного, O(1) для итеративного.

## 3. Псевдокод
```text
Рекурсивный:
Функция search(node, key):
    если node == null или node.key == key: вернуть node
    если key < node.key: вернуть search(node.left, key)
    иначе: вернуть search(node.right, key)

Итеративный:
Функция search_iter(root, key):
    current = root
    пока current != null и current.key != key:
        если key < current.key: current = current.left
        иначе: current = current.right
    вернуть current
```

## 4. Реализация на Python
```python
from typing import Optional

class TreeNode:
    def __init__(self, key: int):
        self.key = key
        self.left: Optional[TreeNode] = None
        self.right: Optional[TreeNode] = None


def search_bst(root: Optional[TreeNode], key: int) -> Optional[TreeNode]:
    if not root or root.key == key:
        return root
    if key < root.key:
        return search_bst(root.left, key)
    return search_bst(root.right, key)


def search_bst_iter(root: Optional[TreeNode], key: int) -> Optional[TreeNode]:
    current = root
    while current and current.key != key:
        current = current.left if key < current.key else current.right
    return current
```


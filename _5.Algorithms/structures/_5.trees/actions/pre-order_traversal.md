**Pre-order обход (Pre-order Traversal)**

## 1. Описание
Алгоритм обхода бинарного дерева, при котором сначала обрабатывается текущий узел, затем рекурсивно обходится левое поддерево, затем правое.

## 2. Сложность
- **Время:** O(n), где n — число узлов в дереве.
- **Память:** O(h) — глубина рекурсии (h — высота дерева).

## 3. Псевдокод
```text
Функция preorder(node):
    если node == null: вернуть
    обработать node.value
    preorder(node.left)
    preorder(node.right)
```

## 4. Итеративный вариант (через стек)
```text
Функция preorder_iter(root):
    если root == null: вернуть
    создать пустой стек S
    S.push(root)
    пока S не пуст:
        node = S.pop()
        обработать node.value
        если node.right != null: S.push(node.right)
        если node.left != null: S.push(node.left)
```

## 5. Реализация на Python
```python
from typing import Optional, List

class TreeNode:
    def __init__(self, val: int):
        self.val = val
        self.left: Optional[TreeNode] = None
        self.right: Optional[TreeNode] = None


def preorder(root: Optional[TreeNode]) -> List[int]:
    """Рекурсивный Pre-order обход."""
    res: List[int] = []
    def dfs(node: Optional[TreeNode]):
        if not node:
            return
        res.append(node.val)
        dfs(node.left)
        dfs(node.right)
    dfs(root)
    return res


def preorder_iter(root: Optional[TreeNode]) -> List[int]:
    """Итеративный Pre-order обход через стек."""
    if not root:
        return []
    res: List[int] = []
    stack: List[TreeNode] = [root]
    while stack:
        node = stack.pop()
        res.append(node.val)
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)
    return res
```


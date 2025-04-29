**Нахождение наименьшего общего предка (LCA)**

## 1. Описание
Поиск узла, который является общим предком двух заданных узлов и находится максимально глубоко.

## 2. Сложность
- **Время:** O(h)
- **Память:** O(h) рекурсия, O(1) итеративный вариант с родительскими указателями.

## 3. Псевдокод (BST)
```text
Функция lca(root, p, q):
    если root.key > p.key и root.key > q.key:
        вернуть lca(root.left, p, q)
    если root.key < p.key и root.key < q.key:
        вернуть lca(root.right, p, q)
    вернуть root
```

## 4. Реализация на Python
```python
from typing import Optional

class TreeNode:
    def __init__(self, key: int):
        self.key = key
        self.left: Optional[TreeNode] = None
        self.right: Optional[TreeNode] = None


def lowest_common_ancestor(root: TreeNode, p: TreeNode, q: TreeNode) -> Optional[TreeNode]:
    """Возвращает LCA двух узлов в BST."""
    if not root:
        return None
    if root.key > p.key and root.key > q.key:
        return lowest_common_ancestor(root.left, p, q)
    if root.key < p.key and root.key < q.key:
        return lowest_common_ancestor(root.right, p, q)
    return root
```


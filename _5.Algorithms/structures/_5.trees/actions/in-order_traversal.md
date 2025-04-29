**In-order обход (In-order Traversal)**

## 1. Описание
Алгоритм обхода бинарного дерева: сначала рекурсивно обходит левое поддерево, затем обрабатывает текущий узел, затем правое.

## 2. Сложность
- **Время:** O(n)
- **Память:** O(h)

## 3. Псевдокод
```text
Функция inorder(node):
    если node == null: вернуть
    inorder(node.left)
    обработать node.value
    inorder(node.right)
```

## 4. Итеративный вариант
```text
Функция inorder_iter(root):
    создать пустой стек S
    current = root
    пока current != null или S не пуст:
        пока current != null:
            S.push(current)
            current = current.left
        current = S.pop()
        обработать current.value
        current = current.right
```

## 5. Реализация на Python
```python
from typing import Optional, List

class TreeNode:
    def __init__(self, val: int):
        self.val = val
        self.left: Optional[TreeNode] = None
        self.right: Optional[TreeNode] = None


def inorder(root: Optional[TreeNode]) -> List[int]:
    res: List[int] = []
    def dfs(node: Optional[TreeNode]):
        if not node:
            return
        dfs(node.left)
        res.append(node.val)
        dfs(node.right)
    dfs(root)
    return res


def inorder_iter(root: Optional[TreeNode]) -> List[int]:
    res: List[int] = []
    stack: List[TreeNode] = []
    current = root
    while current or stack:
        while current:
            stack.append(current)
            current = current.left
        current = stack.pop()
        res.append(current.val)
        current = current.right
    return res
```
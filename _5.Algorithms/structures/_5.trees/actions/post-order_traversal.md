**Post-order обход (Post-order Traversal)**

## 1. Описание
Алгоритм обхода бинарного дерева: рекурсивно обходит левое и правое поддеревья, затем обрабатывает текущий узел.

## 2. Сложность
- **Время:** O(n)
- **Память:** O(h)

## 3. Псевдокод
```text
Функция postorder(node):
    если node == null: вернуть
    postorder(node.left)
    postorder(node.right)
    обработать node.value
```

## 4. Итеративный вариант
```text
Функция postorder_iter(root):
    если root == null: вернуть
    создать два стека S1, S2
    S1.push(root)
    пока S1 не пуст:
        node = S1.pop()
        S2.push(node)
        если node.left: S1.push(node.left)
        если node.right: S1.push(node.right)
    пока S2 не пуст:
        node = S2.pop()
        обработать node.value
```

## 5. Реализация на Python
```python
from typing import Optional, List

class TreeNode:
    def __init__(self, val: int):
        self.val = val
        self.left: Optional[TreeNode] = None
        self.right: Optional[TreeNode] = None


def postorder(root: Optional[TreeNode]) -> List[int]:
    res: List[int] = []
    def dfs(node: Optional[TreeNode]):
        if not node:
            return
        dfs(node.left)
        dfs(node.right)
        res.append(node.val)
    dfs(root)
    return res


def postorder_iter(root: Optional[TreeNode]) -> List[int]:
    if not root:
        return []
    stack1: List[TreeNode] = [root]
    stack2: List[TreeNode] = []
    while stack1:
        node = stack1.pop()
        stack2.append(node)
        if node.left:
            stack1.append(node.left)
        if node.right:
            stack1.append(node.right)
    return [node.val for node in reversed(stack2)]
```


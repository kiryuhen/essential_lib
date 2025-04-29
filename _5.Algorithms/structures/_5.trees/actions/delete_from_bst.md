**Удаление в BST (Delete from Binary Search Tree)**

## 1. Описание
Удаление узла с заданным ключом из бинарного дерева поиска с учетом трёх случаев: лист, один ребёнок, два ребёнка.

## 2. Сложность
- **Время:** O(h)
- **Память:** O(h) рекурсивное.

## 3. Псевдокод
```text
Функция delete(node, key):
    если node == null: вернуть null
    если key < node.key: node.left = delete(node.left, key)
    иначе если key > node.key: node.right = delete(node.right, key)
    иначе:
        если node.left == null: вернуть node.right
        если node.right == null: вернуть node.left
        temp = find_min(node.right)
        node.key = temp.key
        node.right = delete(node.right, temp.key)
    вернуть node

Функция find_min(node):
    пока node.left != null:
        node = node.left
    вернуть node
```

## 4. Реализация на Python
```python
from typing import Optional

class TreeNode:
    def __init__(self, key: int):
        self.key = key
        self.left: Optional[TreeNode] = None
        self.right: Optional[TreeNode] = None


def delete_bst(root: Optional[TreeNode], key: int) -> Optional[TreeNode]:
    if not root:
        return None
    if key < root.key:
        root.left = delete_bst(root.left, key)
    elif key > root.key:
        root.right = delete_bst(root.right, key)
    else:
        if not root.left:
            return root.right
        if not root.right:
            return root.left
        temp = find_min(root.right)
        root.key = temp.key
        root.right = delete_bst(root.right, temp.key)
    return root


def find_min(node: TreeNode) -> TreeNode:
    while node.left:
        node = node.left
    return node
```


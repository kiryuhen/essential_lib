**Правый поворот (Right Rotation) в AVL-дереве**

## 1. Описание
Операция для балансировки AVL-дерева, сдвигающая узел вниз влево, чтобы уменьшить высоту правого поддерева.

## 2. Сложность
- **Время:** O(1)
- **Память:** O(1)

## 3. Псевдокод
```text
Функция rotate_right(y):
    x = y.left
    T2 = x.right
    x.right = y
    y.left = T2
    обновить высоту y и x
    вернуть x
```

## 4. Реализация на Python
```python
from typing import Optional

class AVLNode:
    def __init__(self, key: int):
        self.key = key
        self.left: Optional[AVLNode] = None
        self.right: Optional[AVLNode] = None
        self.height = 1


def rotate_right(y: AVLNode) -> AVLNode:
    x = y.left
    T2 = x.right
    x.right = y
    y.left = T2
    y.height = 1 + max(get_height(y.left), get_height(y.right))
    x.height = 1 + max(get_height(x.left), get_height(x.right))
    return x


def get_height(node: Optional[AVLNode]) -> int:
    return node.height if node else 0
```


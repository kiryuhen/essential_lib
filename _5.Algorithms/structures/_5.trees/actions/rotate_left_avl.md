**Левый поворот (Left Rotation) в AVL-дереве**

## 1. Описание
Операция для балансировки AVL-дерева, сдвигающая узел вниз вправо, чтобы уменьшить высоту левого поддерева.

## 2. Сложность
- **Время:** O(1)
- **Память:** O(1)

## 3. Псевдокод
```text
Функция rotate_left(x):
    y = x.right
    T2 = y.left
    y.left = x
    x.right = T2
    обновить высоту x и y
    вернуть y
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


def rotate_left(x: AVLNode) -> AVLNode:
    y = x.right
    T2 = y.left
    y.left = x
    x.right = T2
    x.height = 1 + max(get_height(x.left), get_height(x.right))
    y.height = 1 + max(get_height(y.left), get_height(y.right))
    return y


def get_height(node: Optional[AVLNode]) -> int:
    return node.height if node else 0
```


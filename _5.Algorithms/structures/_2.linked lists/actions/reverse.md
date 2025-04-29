**Разворот связного списка**

## 1. Описание
Алгоритм меняет порядок элементов односвязного списка на обратный, перенастраивая ссылки узлов.

## 2. Сложность
- **Время:** O(n) — нужно обойти все n узлов.
- **Память:** O(1) — только несколько указателей.

## 3. Псевдокод
```text
Функция reverse(head):
    prev = null
    current = head
    пока current != null:
        next_node = current.next
        current.next = prev
        prev = current
        current = next_node
    вернуть prev  // новая голова списка
```

## 4. Реализация на Python
```python
from typing import Optional

class Node:
    def __init__(self, value: int):
        self.value = value
        self.next: Optional[Node] = None


def reverse_linked_list(head: Optional[Node]) -> Optional[Node]:
    """Переворачивает односвязный список и возвращает новую голову."""
    prev = None
    current = head
    while current:
        next_node = current.next
        current.next = prev
        prev = current
        current = next_node
    return prev
```


**Слияние двух отсортированных списков**

## 1. Описание
Объединяет два упорядоченных односвязных списка в один отсортированный.

## 2. Сложность
- **Время:** O(n + m), где n и m — длины списков.
- **Память:** O(1) — изменение ссылок без выделения новых узлов.

## 3. Псевдокод
```text
Функция merge(l1, l2):
    dummy = Node(0)
    tail = dummy
    пока l1 != null и l2 != null:
        если l1.value <= l2.value:
            tail.next = l1
            l1 = l1.next
        иначе:
            tail.next = l2
            l2 = l2.next
        tail = tail.next
    если l1 != null: tail.next = l1
    иначе: tail.next = l2
    вернуть dummy.next
```

## 4. Реализация на Python
```python
from typing import Optional

class Node:
    def __init__(self, value: int):
        self.value = value
        self.next: Optional[Node] = None


def merge_two_sorted_lists(l1: Optional[Node], l2: Optional[Node]) -> Optional[Node]:
    """Возвращает голову объединённых списков."""
    dummy = Node(0)
    tail = dummy
    while l1 and l2:
        if l1.value <= l2.value:
            tail.next = l1
            l1 = l1.next
        else:
            tail.next = l2
            l2 = l2.next
        tail = tail.next
    tail.next = l1 if l1 else l2
    return dummy.next
```


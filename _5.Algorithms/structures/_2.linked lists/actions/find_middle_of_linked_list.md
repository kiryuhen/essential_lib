**Нахождение середины списка**

## 1. Описание
Алгоритм ищет средний узел односвязного списка с помощью двух указателей: медленного и быстрого.

## 2. Сложность
- **Время:** O(n)
- **Память:** O(1)

## 3. Псевдокод
```text
Функция find_middle(head):
    если head == null: вернуть null
    slow = head
    fast = head
    пока fast != null и fast.next != null:
        slow = slow.next
        fast = fast.next.next
    вернуть slow  // указатель на середину
```

## 4. Реализация на Python
```python
from typing import Optional

class Node:
    def __init__(self, value: int):
        self.value = value
        self.next: Optional[Node] = None


def find_middle(head: Optional[Node]) -> Optional[Node]:
    """Возвращает средний узел списка."""
    if not head:
        return None
    slow = head
    fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow
```


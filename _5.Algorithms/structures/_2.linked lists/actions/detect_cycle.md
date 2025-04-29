**Обнаружение цикла в связном списке**

## 1. Описание
Алгоритм Флойда ("черепаха и заяц") проверяет наличие цикла в односвязном списке.

## 2. Сложность
- **Время:** O(n)
- **Память:** O(1)

## 3. Псевдокод
```text
Функция has_cycle(head):
    if head == null: вернуть false
    slow = head
    fast = head.next
    пока fast != null и fast.next != null:
        если slow == fast: вернуть true
        slow = slow.next
        fast = fast.next.next
    вернуть false
```

## 4. Реализация на Python
```python
from typing import Optional

class Node:
    def __init__(self, value: int):
        self.value = value
        self.next: Optional[Node] = None


def has_cycle(head: Optional[Node]) -> bool:
    """Возвращает True, если в списке есть цикл."""
    if not head:
        return False
    slow = head
    fast = head.next
    while fast and fast.next:
        if slow == fast:
            return True
        slow = slow.next
        fast = fast.next.next
    return False
```


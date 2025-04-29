**Удаление дубликатов в списке**

## 1. Описание
Алгоритм удаляет все узлы с повторяющимися значениями в неупорядоченном односвязном списке, сохраняя только первые вхождения.

## 2. Сложность
- **Время:** O(n)
- **Память:** O(n) — для хранения множества встреченных значений.

## 3. Псевдокод
```text
Функция remove_duplicates(head):
    если head == null: вернуть null
    seen = пустое множество
    prev = null
    current = head
    пока current != null:
        если current.value в seen:
            prev.next = current.next
        иначе:
            добавить current.value в seen
            prev = current
        current = current.next
    вернуть head
```

## 4. Реализация на Python
```python
from typing import Optional

class Node:
    def __init__(self, value: int):
        self.value = value
        self.next: Optional[Node] = None


def remove_duplicates(head: Optional[Node]) -> Optional[Node]:
    """Удаляет дубликаты в неупорядоченном списке."""
    if not head:
        return None
    seen = set()
    prev = None
    current = head
    while current:
        if current.value in seen:
            prev.next = current.next
        else:
            seen.add(current.value)
            prev = current
        current = current.next
    return head
```


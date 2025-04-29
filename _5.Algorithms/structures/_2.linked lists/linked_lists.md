**Связные списки**

## 1. Описание
Связный список — структура данных, состоящая из узлов, каждый из которых содержит значение и ссылку на следующий узел. В односвязном списке узел хранит адрес только следующего; в двусвязном — и предыдущего.

**Цель и применение:**
- Обеспечить динамический размер и эффективные вставки/удаления в середине или начале.
- Используется в реализации стеков, очередей, графов, хеш-таблиц (для цепочек) и многих алгоритмов.

## 2. Плюсы и минусы
**Плюсы:**
- Вставка и удаление узла при известной позиции — O(1).
- Динамическое изменение длины без ресайзинга всего блока памяти.

**Минусы:**
- Доступ по индексу требует перебора от головы — O(n).
- Дополнительная память на хранение ссылок (на следующий/предыдущий узлы).

## 3. Основные операции и их сложности
| Операция                      | Сложность  |
|-------------------------------|------------|
| Поиск по значению             | O(n)       |
| Вставка в начало              | O(1)       |
| Вставка в конец               | O(n)       |
| Удаление узла (по ссылке)     | O(1)       |
| Удаление по значению          | O(n)       |

## 4. Алгоритмы с односвязным списком
- **Разворот списка** (реверс): O(n)
- **Обнаружение цикла** (алгоритм «черепаха и заяц»): O(n)
- **Слияние двух отсортированных списков**: O(n + m)
- **Нахождение середины списка** (два указателя): O(n)
- **Удаление дубликатов** (в упорядоченном или неупорядоченном списке): O(n)

## 5. Псевдокод ключевых алгоритмов
```text
Функция reverse(head):
    prev = null
    current = head
    пока current != null:
        next_node = current.next
        current.next = prev
        prev = current
        current = next_node
    вернуть prev  // новая голова

Функция detect_cycle(head):
    slow = head
    fast = head
    пока fast != null и fast.next != null:
        slow = slow.next
        fast = fast.next.next
        если slow == fast:
            вернуть true
    вернуть false

Функция find_middle(head):
    slow = head
    fast = head
    пока fast != null и fast.next != null:
        slow = slow.next
        fast = fast.next.next
    вернуть slow  // вершина середины
```

## 6. Реализация на Python
```python
class Node:
    """Узел односвязного списка"""
    def __init__(self, value):
        self.value = value
        self.next = None

class LinkedList:
    """Односвязный список"""
    def __init__(self):
        self.head = None

    def insert_at_head(self, value):
        """Вставка нового узла в начало"""
        node = Node(value)
        node.next = self.head
        self.head = node

    def append(self, value):
        """Добавление в конец списка"""
        node = Node(value)
        if not self.head:
            self.head = node
            return
        current = self.head
        while current.next:
            current = current.next
        current.next = node

    def delete(self, value):
        """Удаление первого узла с заданным значением"""
        current = self.head
        prev = None
        while current and current.value != value:
            prev = current
            current = current.next
        if not current:
            return  # элемент не найден
        if not prev:
            self.head = current.next
        else:
            prev.next = current.next

    def search(self, value):
        """Поиск узла по значению"""
        current = self.head
        while current:
            if current.value == value:
                return current
            current = current.next
        return None

    def reverse(self):
        """Разворот списка"""
        prev = None
        current = self.head
        while current:
            nxt = current.next
            current.next = prev
            prev = current
            current = nxt
        self.head = prev

    def detect_cycle(self):
        """Проверка наличия цикла"""
        slow = self.head
        fast = self.head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            if slow is fast:
                return True
        return False

    def find_middle(self):
        """Нахождение середины списка"""
        slow = self.head
        fast = self.head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
        return slow

    def remove_duplicates(self):
        """Удаление дубликатов в неупорядоченном списке"""
        seen = set()
        current = self.head
        prev = None
        while current:
            if current.value in seen:
                prev.next = current.next
            else:
                seen.add(current.value)
                prev = current
            current = current.next
```


**Очереди**

## 1. Описание
Очередь — линейная структура данных, реализующая принцип FIFO (First In, First Out): первый добавленный элемент извлекается первым.

**Цель и применение:**
- Планирование и обработка задач в порядке поступления.
- Обходы графов/деревьев в ширину (BFS).
- Реализация кэширующих механизмов (например, LRU-кэш).

## 2. Плюсы и минусы
**Плюсы:**
- Операции Enqueue/Dequeue выполняются за O(1).
- Натуральная модель для очередей ожидания и задач.

**Минусы:**
- Доступ только к фронту и концу, нет произвольного доступа.
- При naive-реализации на массиве требуется сдвиг элементов на Dequeue, что приводит к O(n).

## 3. Основные операции и их сложности
| Операция     | Сложность |
|--------------|-----------|
| Enqueue      | O(1)      |
| Dequeue      | O(1)      |
| Peek (front) | O(1)      |
| IsEmpty      | O(1)      |

## 4. Алгоритмы и применения
- **Обход в ширину (BFS)** в графах и деревьях.
- **Реализация кэша** (например, LRU-кэш через очередь и хеш-таблицу).
- **Обработка задач** в порядке очереди (например, очереди сообщений).
- **Скользящее окно** для массивов (поддерживает максимум или сумму за последние k элементов).

## 5. Псевдокод основных операций
```text
Процедура enqueue(queue, value):
    queue.tail.next = Node(value)
    queue.tail = queue.tail.next

Функция dequeue(queue):
    если queue.head == null:
        ошибка "Queue underflow"
    value = queue.head.value
    queue.head = queue.head.next
    если queue.head == null:
        queue.tail = null
    вернуть value

Функция peek(queue):
    если queue.head == null:
        ошибка "Queue is empty"
    вернуть queue.head.value
```

## 6. Реализация на Python
```python
class Node:
    """Узел для очереди"""
    def __init__(self, value):
        self.value = value
        self.next = None

class Queue:
    """Очередь на основе связного списка"""
    def __init__(self):
        self.head = None
        self.tail = None

    def enqueue(self, value):
        """Добавить элемент в конец очереди"""
        node = Node(value)
        if not self.tail:
            self.head = node
            self.tail = node
        else:
            self.tail.next = node
            self.tail = node

    def dequeue(self):
        """Удалить и вернуть элемент из начала очереди"""
        if not self.head:
            raise IndexError("dequeue from empty queue")
        value = self.head.value
        self.head = self.head.next
        if not self.head:
            self.tail = None
        return value

    def peek(self):
        """Вернуть первый элемент, не удаляя его"""
        if not self.head:
            raise IndexError("peek from empty queue")
        return self.head.value

    def is_empty(self):
        """Проверить, пуста ли очередь"""
        return self.head is None

    def size(self):
        """Вернуть число элементов в очереди"""
        count = 0
        current = self.head
        while current:
            count += 1
            current = current.next
        return count
```


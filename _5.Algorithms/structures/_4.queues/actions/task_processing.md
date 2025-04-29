**Обработка задач в порядке очереди**

## 1. Описание
Простейший алгоритм обработки поступающих задач или сообщений в порядке их получения (FIFO), используя очередь.

## 2. Сложность
- **Время:** O(1) для `enqueue` и `dequeue` (амортизированное при реализации на списке/deque).
- **Память:** O(n) — для хранения задач.

## 3. Псевдокод
```text
Процедура process_tasks(tasks):
    создать пустую очередь Q
    для каждой задачи t в tasks:
        Q.enqueue(t)
    пока Q не пуста:
        task = Q.dequeue()
        обработать task
```

## 4. Реализация на Python
```python
from collections import deque
from typing import Any, Iterable

def process_tasks(tasks: Iterable[Any]) -> None:
    """
    Обрабатывает задачи в порядке поступления.
    """
    queue = deque(tasks)
    while queue:
        task = queue.popleft()
        # здесь должна быть логика обработки task
        print(f"Processing {task}")
```


**Реализация LRU-кэша**

## 1. Описание
LRU (Least Recently Used) — кэш, удаляющий наименее недавно использованный элемент при переполнении. Для реализации используется двухсвязный список для порядка и хеш-таблица для быстрого доступа.

## 2. Сложность
- **Время:** O(1) для операций `get` и `put`.
- **Память:** O(capacity)

## 3. Псевдокод
```text
Класс LRUCache(capacity):
    создать пустой словарь cache
    создать пустой двухсвязный список order
    установить capacity

Функция get(key):
    если key не в cache: вернуть -1
    узел = cache[key]
    удалить узел из списка order
    вставить узел в начало списка order
    вернуть узел.value

Функция put(key, value):
    если key в cache:
        узел = cache[key]
        узел.value = value
        удалить узел из order
    иначе:
        если размер cache == capacity:
            удалить последний узел из order и из cache
        создать новый узел (key, value)
        cache[key] = узел
    вставить узел в начало списка order
```

## 4. Реализация на Python
```python
from collections import OrderedDict

class LRUCache:
    """LRU-кэш с использованием OrderedDict."""
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = OrderedDict()

    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        # переместить в конец как самый недавно использованный
        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            # обновить и переместить
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            # удалить наименее недавно использованный (первый)
            self.cache.popitem(last=False)
```


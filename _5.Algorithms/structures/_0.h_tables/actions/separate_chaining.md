**Метод цепочек (Separate Chaining)**

## 1. Описание
Стратегия разрешения коллизий в хеш-таблице, при которой каждая ячейка массива хранит ссылку на связный список (или другой контейнер), содержащий все элементы с одинаковым хеш-индексом.

## 2. Сложность
- **Время (усреднённое):**
  - Вставка: O(1) + O(length(chain)) ≈ O(1 + α) где α — коэффициент загрузки.
  - Поиск: O(1 + α)
  - Удаление: O(1 + α)
- **Память:** O(n + m) где n — число элементов, m — размер массива.

## 3. Псевдокод
```text
Функция put(key, value):
    idx = hash(key) mod capacity
    для элемента e в table[idx]:
        если e.key == key:
            e.value = value
            вернуть
    добавить (key, value) в список table[idx]

Функция get(key):
    idx = hash(key) mod capacity
    для элемента e в table[idx]:
        если e.key == key:
            вернуть e.value
    вернуть null

Функция delete(key):
    idx = hash(key) mod capacity
    для элемента e в table[idx]:
        если e.key == key:
            удалить e из table[idx]
            вернуть
```

## 4. Реализация на Python
```python
from typing import List, Optional

class HashEntry:
    def __init__(self, key, value):
        self.key = key
        self.value = value

class HashTableSeparateChaining:
    def __init__(self, capacity: int = 8):
        self.capacity = capacity
        self.table: List[List[HashEntry]] = [[] for _ in range(capacity)]
        self.size = 0

    def _index(self, key) -> int:
        return hash(key) % self.capacity

    def put(self, key, value) -> None:
        idx = self._index(key)
        for entry in self.table[idx]:
            if entry.key == key:
                entry.value = value
                return
        self.table[idx].append(HashEntry(key, value))
        self.size += 1

    def get(self, key) -> Optional:
        idx = self._index(key)
        for entry in self.table[idx]:
            if entry.key == key:
                return entry.value
        return None

    def delete(self, key) -> None:
        idx = self._index(key)
        bucket = self.table[idx]
        for i, entry in enumerate(bucket):
            if entry.key == key:
                bucket.pop(i)
                self.size -= 1
                return
```


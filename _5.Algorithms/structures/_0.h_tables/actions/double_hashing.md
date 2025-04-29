**Открытая адресация: двойное хеширование (Double Hashing)**

## 1. Описание
Метод открытой адресации, в котором шаг при коллизии рассчитывается второй хеш-функцией h2, обеспечивая более равномерное распределение по таблице.

## 2. Сложность
- **Время (усреднённое):** O(1) для хороших хеш-функций.
- **Память:** O(capacity)

## 3. Псевдокод
```text
Функция put(key, value):
    h1 = hash1(key) mod capacity
    h2 = 1 + (hash2(key) mod (capacity - 1))
    i = 0
    пока i < capacity:
        idx = (h1 + i*h2) mod capacity
        если table[idx] пуст или ключ совпадает:
            table[idx] = (key, value)
            вернуть
        i += 1
    ошибка "Таблица заполнена"

Функция get(key):
    вычислить h1, h2 как выше
    i = 0
    пока i < capacity:
        idx = (h1 + i*h2) mod capacity
        если table[idx] пуст: вернуть null
        если ключ совпадает: вернуть значение
        i += 1
    вернуть null
```
## 4. Реализация на Python
```python
from typing import Any

class HashTableDoubleHashing:
    def __init__(self, capacity: int = 8):
        self.capacity = capacity
        self.table: list[Any] = [None] * capacity
        self.size = 0

    def _hash1(self, key) -> int:
        return hash(key) % self.capacity

    def _hash2(self, key) -> int:
        return 1 + (hash(key) % (self.capacity - 1))

    def put(self, key, value):
        h1 = self._hash1(key)
        h2 = self._hash2(key)
        i = 0
        while i < self.capacity:
            idx = (h1 + i * h2) % self.capacity
            entry = self.table[idx]
            if entry is None or entry[0] == key:
                if entry is None:
                    self.size += 1
                self.table[idx] = (key, value)
                return
            i += 1
        raise Exception("Hash table is full")

    def get(self, key) -> Any:
        h1 = self._hash1(key)
        h2 = self._hash2(key)
        i = 0
        while i < self.capacity:
            idx = (h1 + i * h2) % self.capacity
            entry = self.table[idx]
            if entry is None:
                return None
            if entry[0] == key:
                return entry[1]
            i += 1
        return None
```


**Динамическое изменение размера (Resizing)**

## 1. Описание
При достижении порога загрузки (например, α > 0.7) хеш-таблица создаёт новый массив вдвое большего размера и ре-хеширует все существующие элементы.

## 2. Сложность
- **Время амортизированное:** O(1) для вставки.
- **Время при ресайзинге:** O(n) для перераспределения n элементов.
- **Память:** O(n)

## 3. Псевдокод
```text
Функция put(key, value):
    если size+1 > threshold * capacity:
        resize()
    выполнить вставку как обычно

Функция resize():
    old_table = table
    capacity = capacity * 2
    создать новый пустой table размера capacity
    size = 0
    для каждого bucket или ячейки в old_table:
        для каждого (k, v) в bucket:
            put(k, v)  // повторная вставка
```

## 4. Реализация на Python
```python
from typing import Any, List, Optional

class HashTableResizing:
    def __init__(self, capacity: int = 8, load_factor: float = 0.7):
        self.capacity = capacity
        self.load_factor = load_factor
        self.size = 0
        self.table: List[Optional[tuple[Any, Any]]] = [None] * capacity

    def _hash(self, key) -> int:
        return hash(key) % self.capacity

    def put(self, key, value) -> None:
        if self.size + 1 > self.capacity * self.load_factor:
            self._resize()
        idx = self._hash(key)
        while self.table[idx] is not None and self.table[idx][0] != key:
            idx = (idx + 1) % self.capacity
        if self.table[idx] is None:
            self.size += 1
        self.table[idx] = (key, value)

    def get(self, key) -> Optional[Any]:
        idx = self._hash(key)
        while self.table[idx] is not None:
            if self.table[idx][0] == key:
                return self.table[idx][1]
            idx = (idx + 1) % self.capacity
        return None

    def delete(self, key) -> None:
        idx = self._hash(key)
        while self.table[idx] is not None:
            if self.table[idx][0] == key:
                self.table[idx] = None
                self.size -= 1
                break
            idx = (idx + 1) % self.capacity

    def _resize(self) -> None:
        old_table = self.table
        self.capacity *= 2
        self.table = [None] * self.capacity
        old_size = self.size
        self.size = 0
        for entry in old_table:
            if entry is not None:
                self.put(entry[0], entry[1])
        self.size = old_size
```


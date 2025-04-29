**Открытая адресация: линейное пробирование (Linear Probing)**

## 1. Описание
Метод открытой адресации для разрешения коллизий, при котором в случае занятости ячейки idx проверяется следующая (idx+1) мод capacity, и так далее.

## 2. Сложность
- **Время (усреднённое):** O(1) при малой загрузке, ухудшается при большом коэффициенте загрузки α.
- **Память:** O(capacity)

## 3. Псевдокод
```text
Функция put(key, value):
    i = 0
    повторять до capacity раз:
        idx = (hash(key) + i) mod capacity
        если table[idx] пуст или table[idx].key == key:
            table[idx] = (key, value)
            вернуть
        i += 1
    ошибка "Таблица заполнена"

Функция get(key):
    i = 0
    повторять до capacity раз:
        idx = (hash(key) + i) mod capacity
        если table[idx] пуст: вернуть null
        если table[idx].key == key: вернуть table[idx].value
        i += 1
    вернуть null

Функция delete(key):
    найти индекс idx аналогично get
    установить table[idx] = DELETED_MARKER
```

## 4. Реализация на Python
```python
from typing import Any

DELETED = object()

class HashTableLinearProbing:
    def __init__(self, capacity: int = 8):
        self.capacity = capacity
        self.table: list[Any] = [None] * capacity
        self.size = 0

    def _probe(self, key):
        i = 0
        while i < self.capacity:
            idx = (hash(key) + i) % self.capacity
            yield idx
            i += 1

    def put(self, key, value):
        for idx in self._probe(key):
            entry = self.table[idx]
            if entry is None or entry is DELETED or entry[0] == key:
                self.table[idx] = (key, value)
                if entry is None or entry is DELETED:
                    self.size += 1
                return
        raise Exception("Hash table is full")

    def get(self, key) -> Any:
        for idx in self._probe(key):
            entry = self.table[idx]
            if entry is None:
                return None
            if entry is not DELETED and entry[0] == key:
                return entry[1]
        return None

    def delete(self, key):
        for idx in self._probe(key):
            entry = self.table[idx]
            if entry is None:
                return
            if entry is not DELETED and entry[0] == key:
                self.table[idx] = DELETED
                self.size -= 1
                return
```


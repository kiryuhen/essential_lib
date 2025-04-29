**Хеш-таблицы**

## 1. Описание
Хеш-таблица — структура данных для ассоциативного массива (ключ → значение), обеспечивающая среднее время операций O(1) с помощью хеш-функции.

**Цель и применение:**
- Быстрый поиск, вставка и удаление по ключу.
- Используется в базах данных, кэшировании, индексировании.

## 2. Плюсы и минусы
**Плюсы:**
- Операции вставки, поиска, удаления за O(1) в среднем.
- Гибкость ключей (строки, числа).

**Минусы:**
- Возможны коллизии, требующие разрешения.
- В худшем случае (плохая хеш-функция) сложность может вырасти до O(n).

## 3. Основные операции и их сложности
| Операция     | Сложность       |
|--------------|-----------------|
| Вставка      | O(1) амортизированно |
| Поиск        | O(1) амортизированно |
| Удаление     | O(1) амортизированно |

## 4. Разрешение коллизий
- **Метод цепочек**: каждая ячейка хранит список пар (ключ, значение).
- **Открытая адресация**:
  - Линейное пробирование
  - Двойное хеширование
- **Ресайзинг**: при загрузке > threshold создаётся новый массив вдвое большего размера и все элементы перераспределяются.

## 5. Псевдокод основных операций
```text
Функция put(key, value):
    idx = hash(key) mod capacity
    пока table[idx] занят и table[idx].key != key:
        idx = (idx + 1) mod capacity  // линейное пробирование
    table[idx] = (key, value)

Функция get(key):
    idx = hash(key) mod capacity
    пока table[idx] не пуст:
        если table[idx].key == key:
            вернуть table[idx].value
        idx = (idx + 1) mod capacity
    вернуть null
```

## 6. Реализация на Python
```python
class HashTable:
    """Хеш-таблица с открытой адресацией"""
    def __init__(self, capacity=8):
        self.capacity = capacity
        self.size = 0
        self.keys = [None] * capacity
        self.values = [None] * capacity

    def _hash(self, key, i=0):
        return (hash(key) + i) % self.capacity

    def put(self, key, value):
        i = 0
        while i < self.capacity:
            idx = self._hash(key, i)
            if self.keys[idx] is None or self.keys[idx] == key:
                self.keys[idx] = key
                self.values[idx] = value
                if i == 0:  # новая вставка
                    self.size += 1
                break
            i += 1
        if self.size / self.capacity > 0.7:
            self._resize()

    def get(self, key):
        i = 0
        while i < self.capacity:
            idx = self._hash(key, i)
            if self.keys[idx] == key:
                return self.values[idx]
            if self.keys[idx] is None:
                return None
            i += 1
        return None

    def delete(self, key):
        i = 0
        while i < self.capacity:
            idx = self._hash(key, i)
            if self.keys[idx] == key:
                self.keys[idx] = None
                self.values[idx] = None
                self.size -= 1
                return
            if self.keys[idx] is None:
                return
            i += 1

    def _resize(self):
        old_keys = self.keys
        old_values = self.values
        self.capacity *= 2
        self.keys = [None] * self.capacity
        self.values = [None] * self.capacity
        self.size = 0
        for k, v in zip(old_keys, old_values):
            if k is not None:
                self.put(k, v)
```


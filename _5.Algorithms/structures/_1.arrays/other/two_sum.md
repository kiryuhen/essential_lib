**Нахождение двух элементов с заданной суммой**

## 1. Описание
Задача: найти в массиве два элемента, сумма которых равна заданному значению.

## 2. Сложность
- **Время:** O(n)
- **Память:** O(n)

## 3. Псевдокод
```text
Функция two_sum(array, target):
    словарь seen
    для i от 0 до length(array)-1:
        complement = target - array[i]
        если complement в seen:
            вернуть (seen[complement], i)
        seen[array[i]] = i
    вернуть None  // пара не найдена
```

## 4. Реализация на Python
```python
from typing import List, Optional, Tuple

def two_sum(arr: List[int], target: int) -> Optional[Tuple[int, int]]:
    """
    Возвращает индексы двух чисел, сумма которых равна target, или None.
    """
    seen: dict[int, int] = {}
    for i, num in enumerate(arr):
        complement = target - num
        if complement in seen:
            return (seen[complement], i)
        seen[num] = i
    return None
```


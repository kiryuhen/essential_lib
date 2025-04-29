**Алгоритм Кадане**

## 1. Описание
Эффективный алгоритм поиска подмассива с максимальной суммой (максимальной субструктуры) в одномерном массиве.

## 2. Сложность
- **Время:** O(n)
- **Память:** O(1)

## 3. Псевдокод
```text
Функция kadane(array):
    max_current = array[0]
    max_global = array[0]
    для i от 1 до length(array)-1:
        max_current = max(array[i], max_current + array[i])
        если max_current > max_global:
            max_global = max_current
    вернуть max_global
```

## 4. Реализация на Python
```python
from typing import List

def kadane(arr: List[int]) -> int:
    """
    Возвращает максимальную сумму подмассива.
    """
    max_current = max_global = arr[0]
    for x in arr[1:]:
        max_current = max(x, max_current + x)
        if max_current > max_global:
            max_global = max_current
    return max_global
```


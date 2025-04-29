**Сортировка вставками**

## 1. Описание

Алгоритм сортировки, строящий отсортированную часть массива поэлементно, вставляя каждый новый элемент в нужную позицию в уже отсортированных.

## 2. Сложность

- **Время:** O(n²)
- **Память:** O(1)

## 3. Псевдокод

```text
Функция insertion_sort(array):
    для i от 1 до length(array)-1:
        key = array[i]
        j = i - 1
        пока j >= 0 и array[j] > key:
            array[j+1] = array[j]
            j = j - 1
        array[j+1] = key
```

## 4. Реализация на Python

```python
from typing import List

def insertion_sort(arr: List[int]) -> None:
    """
    Сортировка списка на месте методом вставок.
    """
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1
        arr[j + 1] = key
```


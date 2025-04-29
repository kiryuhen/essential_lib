**Сортировка слиянием**

## 1. Описание
Разделяй и властвуй: рекурсивно разбивает массив пополам до единичных элементов, затем сливает две отсортированные половины.

## 2. Сложность
- **Время:** O(n log n)
- **Память:** O(n) для временных массивов при слиянии.

## 3. Псевдокод
```text
Функция merge_sort(array):
    если length(array) > 1:
        mid = length(array) // 2
        L = array[0..mid-1]
        R = array[mid..end]
        merge_sort(L)
        merge_sort(R)
        i = j = k = 0
        пока i < length(L) и j < length(R):
            если L[i] < R[j]:
                array[k] = L[i]; i += 1
            иначе:
                array[k] = R[j]; j += 1
            k += 1
        пока i < length(L):
            array[k] = L[i]; i += 1; k += 1
        пока j < length(R):
            array[k] = R[j]; j += 1; k += 1
```

## 4. Реализация на Python
```python
from typing import List

def merge_sort(arr: List[int]) -> None:
    """
    Сортировка списка на месте слиянием.
    """
    if len(arr) > 1:
        mid = len(arr) // 2
        L = arr[:mid]
        R = arr[mid:]
        merge_sort(L)
        merge_sort(R)
        i = j = k = 0
        while i < len(L) and j < len(R):
            if L[i] < R[j]:
                arr[k] = L[i]
                i += 1
            else:
                arr[k] = R[j]
                j += 1
            k += 1
        while i < len(L):
            arr[k] = L[i]
            i += 1; k += 1
        while j < len(R):
            arr[k] = R[j]
            j += 1; k += 1
```


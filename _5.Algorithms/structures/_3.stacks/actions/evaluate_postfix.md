**Вычисление выражений в постфиксной записи**

## 1. Описание
Алгоритм вычисляет арифметическое выражение, заданное в обратной польской нотации (операнды перед операторами), с помощью стека.

## 2. Сложность
- **Время:** O(n) — один проход по записи длины n.
- **Память:** O(n) — стек для операндов.

## 3. Псевдокод
```text
Функция eval_postfix(tokens):
    создать пустой стек
    для каждого токена t в tokens:
        если t — число:
            стек.push(t)
        иначе если t — оператор (+, -, *, /):
            b = стек.pop()
            a = стек.pop()
            результат = apply_operator(a, b, t)
            стек.push(результат)
    вернуть стек.pop()
```

## 4. Реализация на Python
```python
from typing import List, Union

def eval_postfix(tokens: List[str]) -> Union[int, float]:
    """
    Вычисляет выражение в обратной польской записи.
    Поддерживает операторы +, -, *, /.
    """
    stack: List[Union[int, float]] = []
    for t in tokens:
        if t not in '+-*/':
            stack.append(int(t))
        else:
            b = stack.pop()
            a = stack.pop()
            if t == '+':
                stack.append(a + b)
            elif t == '-':
                stack.append(a - b)
            elif t == '*':
                stack.append(a * b)
            elif t == '/':
                stack.append(a / b)
    return stack.pop()
```


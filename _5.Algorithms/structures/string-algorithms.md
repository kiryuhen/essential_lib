# Строковые алгоритмы

Строковые алгоритмы играют фундаментальную роль в программировании и обработке данных. Они используются для поиска, сравнения, классификации и трансформации текстовой информации. В этом разделе мы рассмотрим ключевые алгоритмы для работы со строками, их реализацию и практическое применение.

## Алгоритмы поиска подстроки

Поиск подстроки (или образца) в строке — одна из самых распространенных задач обработки текста. В общем виде задача формулируется так: имеется строка T (текст) длиной n и строка P (образец, паттерн) длиной m, необходимо найти все вхождения образца P в текст T.

### 1. Наивный алгоритм поиска подстроки

Наивный подход заключается в последовательном "скольжении" окна длиной m по тексту T и сравнении содержимого окна с образцом P.

```python
def naive_search(text, pattern):
    """
    Наивный алгоритм поиска всех вхождений образца в текст.
    
    Args:
        text: Строка, в которой производится поиск (длина n)
        pattern: Искомый образец (длина m)
        
    Returns:
        Список индексов, с которых начинаются вхождения образца в текст
    """
    n = len(text)
    m = len(pattern)
    result = []
    
    # Проверяем все возможные позиции для начала образца
    for i in range(n - m + 1):
        # Предполагаем, что образец совпадает с текущей позиции
        match = True
        
        # Проверяем каждый символ образца
        for j in range(m):
            if text[i + j] != pattern[j]:
                match = False
                break
        
        # Если все символы совпали, добавляем индекс в результат
        if match:
            result.append(i)
    
    return result

# Пример использования
text = "ABABDABACDABABCABAB"
pattern = "ABABC"
indices = naive_search(text, pattern)
print(f"Образец '{pattern}' найден в позициях: {indices}")
```

**Сложность алгоритма**:
- Временная сложность: O(n*m) в худшем случае
- Пространственная сложность: O(1) (не считая выходной список)

Наивный алгоритм прост в реализации, но неэффективен для длинных строк и образцов, так как при каждом несовпадении он возвращается в начало образца и начинает сравнение заново со следующей позиции текста.

### 2. Алгоритм Кнута-Морриса-Пратта (KMP)

Алгоритм Кнута-Морриса-Пратта (KMP) — это эффективный алгоритм поиска подстроки, который использует информацию о самом образце для оптимизации процесса поиска. Он избегает ненужных сравнений, запоминая, как далеко нужно вернуться при несовпадении.

```python
def kmp_search(text, pattern):
    """
    Алгоритм Кнута-Морриса-Пратта для поиска всех вхождений образца в текст.
    
    Args:
        text: Строка, в которой производится поиск
        pattern: Искомый образец
        
    Returns:
        Список индексов, с которых начинаются вхождения образца в текст
    """
    n = len(text)
    m = len(pattern)
    
    # Если образец пустой, возвращаем все позиции
    if m == 0:
        return list(range(n + 1))
    
    # Строим таблицу префикс-функции для образца
    lps = compute_lps(pattern)
    
    result = []
    i = 0  # индекс для текста
    j = 0  # индекс для образца
    
    while i < n:
        # Если текущие символы совпадают, продвигаемся вперед
        if pattern[j] == text[i]:
            i += 1
            j += 1
        
        # Если достигли конца образца, значит нашли совпадение
        if j == m:
            result.append(i - j)
            # Используем значение префикс-функции для продолжения поиска
            j = lps[j - 1]
        # Если символы не совпадают
        elif i < n and pattern[j] != text[i]:
            # Если j > 0, используем префикс-функцию
            if j > 0:
                j = lps[j - 1]
            # Иначе просто переходим к следующему символу текста
            else:
                i += 1
    
    return result

def compute_lps(pattern):
    """
    Вычисляет массив длин наибольших собственных префиксов-суффиксов (LPS)
    для алгоритма KMP.
    
    Args:
        pattern: Образец для вычисления LPS
        
    Returns:
        Массив LPS, где lps[i] — длина наибольшего собственного префикса-суффикса 
        для подстроки pattern[0...i]
    """
    m = len(pattern)
    lps = [0] * m  # Инициализируем массив LPS нулями
    
    # Длина предыдущего наидлиннейшего префикса-суффикса
    length = 0
    i = 1
    
    # Вычисляем lps[i] для i от 1 до m-1
    while i < m:
        if pattern[i] == pattern[length]:
            length += 1
            lps[i] = length
            i += 1
        else:
            # Если символы не совпадают
            if length != 0:
                # Используем предыдущее значение lps
                length = lps[length - 1]
            else:
                # Если length = 0, то lps[i] = 0
                lps[i] = 0
                i += 1
    
    return lps

# Пример использования
text = "ABABDABACDABABCABAB"
pattern = "ABABC"
indices = kmp_search(text, pattern)
print(f"Образец '{pattern}' найден в позициях (KMP): {indices}")
```

**Сложность алгоритма KMP**:
- Временная сложность: O(n + m)
- Пространственная сложность: O(m)

Алгоритм KMP эффективнее наивного подхода, так как он не возвращается назад по тексту и использует информацию о совпавших символах для оптимизации поиска.

### 3. Алгоритм Бойера-Мура

Алгоритм Бойера-Мура — ещё один эффективный алгоритм поиска подстроки, который часто работает быстрее KMP на практике. Он начинает сравнение с конца образца и может пропускать целые участки текста при несовпадении.

```python
def boyer_moore_search(text, pattern):
    """
    Алгоритм Бойера-Мура для поиска всех вхождений образца в текст.
    
    Args:
        text: Строка, в которой производится поиск
        pattern: Искомый образец
        
    Returns:
        Список индексов, с которых начинаются вхождения образца в текст
    """
    n = len(text)
    m = len(pattern)
    
    if m == 0:
        return list(range(n + 1))
    
    # Строим таблицу плохого символа
    bad_char = build_bad_char_table(pattern)
    
    # Строим таблицу хорошего суффикса
    good_suffix = build_good_suffix_table(pattern)
    
    result = []
    i = 0  # Позиция начала текущего сопоставления в тексте
    
    while i <= n - m:
        j = m - 1  # Начинаем с конца образца
        
        # Сравниваем символы справа налево
        while j >= 0 and pattern[j] == text[i + j]:
            j -= 1
        
        # Если образец полностью совпал
        if j < 0:
            result.append(i)
            # Смещаемся на 1 или на значение из таблицы хорошего суффикса
            i += max(1, good_suffix[0])
        else:
            # Вычисляем смещение на основе правил плохого символа и хорошего суффикса
            char_skip = bad_char.get(text[i + j], m)
            suffix_skip = good_suffix[j]
            
            # Выбираем максимальное смещение
            i += max(char_skip - (m - 1 - j), suffix_skip)
    
    return result

def build_bad_char_table(pattern):
    """
    Строит таблицу плохого символа для алгоритма Бойера-Мура.
    
    Args:
        pattern: Образец
        
    Returns:
        Словарь, где ключи — символы, а значения — смещения
    """
    m = len(pattern)
    table = {}
    
    # Для каждого символа в образце запоминаем его самую правую позицию
    for i in range(m - 1):
        table[pattern[i]] = m - 1 - i
    
    return table

def build_good_suffix_table(pattern):
    """
    Строит таблицу хорошего суффикса для алгоритма Бойера-Мура.
    
    Args:
        pattern: Образец
        
    Returns:
        Массив смещений для каждой позиции образца
    """
    m = len(pattern)
    # Инициализируем таблицу смещений значением m
    table = [m] * m
    
    # Строим вспомогательный массив для определения максимальных суффиксов
    suffix_length = [0] * m
    compute_suffix_length(pattern, suffix_length)
    
    # Случай 1: Суффикс не встречается как префикс в образце
    j = 0
    for i in range(m - 1, -1, -1):
        if suffix_length[i] == i + 1:
            for j in range(m - 1 - i):
                if table[j] == m:
                    table[j] = m - 1 - i
    
    # Случай 2: Суффикс встречается в образце
    for i in range(m - 1):
        table[m - 1 - suffix_length[i]] = m - 1 - i
    
    return table

def compute_suffix_length(pattern, suffix_length):
    """
    Вычисляет длину максимального суффикса для каждой позиции образца.
    
    Args:
        pattern: Образец
        suffix_length: Массив для записи длин суффиксов
    """
    m = len(pattern)
    suffix_length[m - 1] = m
    
    j = m - 1
    for i in range(m - 2, -1, -1):
        while j < m - 1 and pattern[i] != pattern[j]:
            j = m - 1 - suffix_length[j]
        j -= 1
        suffix_length[i] = m - 1 - j
    
    return suffix_length

# Пример использования
text = "ABABDABACDABABCABAB"
pattern = "ABABC"
indices = boyer_moore_search(text, pattern)
print(f"Образец '{pattern}' найден в позициях (Бойер-Мур): {indices}")
```

**Сложность алгоритма Бойера-Мура**:
- Временная сложность: O(n*m) в худшем случае, но на практике обычно O(n/m)
- Пространственная сложность: O(m + k), где k — размер алфавита

Алгоритм Бойера-Мура особенно эффективен для длинных образцов и больших алфавитов, так как он может пропускать большие участки текста.

### 4. Алгоритм Рабина-Карпа

Алгоритм Рабина-Карпа использует хеширование для поиска подстроки. Вместо прямого сравнения символов, он вычисляет хеш-значения для образца и для подстрок текста той же длины, что позволяет быстро отфильтровать несовпадающие подстроки.

```python
def rabin_karp_search(text, pattern):
    """
    Алгоритм Рабина-Карпа для поиска всех вхождений образца в текст.
    
    Args:
        text: Строка, в которой производится поиск
        pattern: Искомый образец
        
    Returns:
        Список индексов, с которых начинаются вхождения образца в текст
    """
    n = len(text)
    m = len(pattern)
    result = []
    
    if m == 0 or m > n:
        return result
    
    # Для простоты используем простое число 101 и модуль 2^31 - 1
    d = 256  # Размер алфавита (ASCII)
    q = 2**31 - 1  # Большое простое число
    
    # Вычисляем d^(m-1) % q для использования при обновлении хеша
    h = 1
    for _ in range(m - 1):
        h = (h * d) % q
    
    # Вычисляем хеш-значения для образца и первого окна текста
    p = 0  # Хеш для образца
    t = 0  # Хеш для текущего окна текста
    
    for i in range(m):
        p = (d * p + ord(pattern[i])) % q
        t = (d * t + ord(text[i])) % q
    
    # Скользим окно по тексту
    for i in range(n - m + 1):
        # Если хеш-значения совпадают, проверяем символы
        if p == t:
            # Проверяем посимвольно
            match = True
            for j in range(m):
                if text[i + j] != pattern[j]:
                    match = False
                    break
            
            if match:
                result.append(i)
        
        # Вычисляем хеш для следующего окна
        if i < n - m:
            # Удаляем старший разряд и добавляем новый младший разряд
            t = (d * (t - ord(text[i]) * h) + ord(text[i + m])) % q
            
            # В случае отрицательного значения
            if t < 0:
                t += q
    
    return result

# Пример использования
text = "ABABDABACDABABCABAB"
pattern = "ABABC"
indices = rabin_karp_search(text, pattern)
print(f"Образец '{pattern}' найден в позициях (Рабин-Карп): {indices}")
```

**Сложность алгоритма Рабина-Карпа**:
- Временная сложность: O(n*m) в худшем случае, O(n+m) в среднем и лучшем случае
- Пространственная сложность: O(1)

Алгоритм Рабина-Карпа особенно полезен для поиска нескольких образцов одновременно (используя множество хешей) и для поиска в многомерных структурах данных.

### 5. Алгоритм Z-функции

Z-функция (или Z-алгоритм) — это эффективный алгоритм для решения различных задач поиска строк. Z-функция строки S — это массив Z, где Z[i] — это длина наибольшего префикса подстроки S[i...], совпадающего с префиксом всей строки S.

```python
def z_function(s):
    """
    Вычисляет Z-функцию для строки s.
    
    Args:
        s: Входная строка
        
    Returns:
        Массив Z, где Z[i] — длина наибольшего общего префикса s и s[i:]
    """
    n = len(s)
    z = [0] * n
    
    # [l, r] — это текущий Z-блок
    l, r = 0, 0
    
    for i in range(1, n):
        # Если i находится внутри Z-блока, используем предыдущие вычисления
        if i <= r:
            z[i] = min(r - i + 1, z[i - l])
        
        # Расширяем Z-значение явной проверкой символов
        while i + z[i] < n and s[z[i]] == s[i + z[i]]:
            z[i] += 1
        
        # Обновляем Z-блок, если нашли более длинный префикс
        if i + z[i] - 1 > r:
            l = i
            r = i + z[i] - 1
    
    return z

def z_search(text, pattern):
    """
    Использует Z-функцию для поиска всех вхождений образца в текст.
    
    Args:
        text: Строка, в которой производится поиск
        pattern: Искомый образец
        
    Returns:
        Список индексов, с которых начинаются вхождения образца в текст
    """
    # Формируем строку pattern$text, где $ — разделитель
    combined = pattern + "$" + text
    z = z_function(combined)
    
    result = []
    pattern_length = len(pattern)
    
    # Ищем Z-значения, равные длине образца
    for i in range(pattern_length + 1, len(combined)):
        if z[i] == pattern_length:
            result.append(i - pattern_length - 1)  # Учитываем смещение из-за pattern$
    
    return result

# Пример использования
text = "ABABDABACDABABCABAB"
pattern = "ABABC"
indices = z_search(text, pattern)
print(f"Образец '{pattern}' найден в позициях (Z-функция): {indices}")
```

**Сложность алгоритма Z-функции**:
- Временная сложность: O(n + m)
- Пространственная сложность: O(n + m)

Z-функция является мощным инструментом для различных задач обработки строк, включая поиск всех вхождений образца, поиск наименьшего периода строки и поиск палиндромов.

### 6. Алгоритм Ахо-Корасик

Алгоритм Ахо-Корасик — это алгоритм для одновременного поиска множества строк в тексте. Он использует структуру данных, похожую на префиксное дерево (бор), дополненную "переходами неудач".

```python
class AhoCorasick:
    """
    Реализация алгоритма Ахо-Корасик для множественного поиска строк.
    """
    def __init__(self):
        self.trie = {}  # Префиксное дерево
        self.fail = {}  # Функция неудачи
        self.output = {}  # Словарь выходных функций
        self.patterns = []  # Список образцов
    
    def add_pattern(self, pattern, pattern_index=None):
        """
        Добавляет образец в автомат.
        
        Args:
            pattern: Строка-образец
            pattern_index: Идентификатор образца (по умолчанию индекс в списке)
        """
        if pattern_index is None:
            pattern_index = len(self.patterns)
        
        self.patterns.append((pattern, pattern_index))
        
        # Добавляем паттерн в префиксное дерево
        node = 0
        for char in pattern:
            if node not in self.trie:
                self.trie[node] = {}
            if char not in self.trie[node]:
                self.trie[node][char] = len(self.trie)
            node = self.trie[node][char]
        
        # Помечаем конечный узел образца
        if node not in self.output:
            self.output[node] = []
        self.output[node].append(pattern_index)
    
    def build_failure_function(self):
        """
        Строит функцию неудачи для автомата Ахо-Корасик.
        """
        # Инициализируем функцию неудачи для корня и его непосредственных потомков
        self.fail[0] = 0
        queue = []
        
        # Добавляем в очередь всех детей корня
        if 0 in self.trie:
            for char, node in self.trie[0].items():
                self.fail[node] = 0
                queue.append(node)
        
        # BFS для построения функции неудачи
        while queue:
            current = queue.pop(0)
            
            # Для каждого символа перехода из текущего состояния
            if current in self.trie:
                for char, node in self.trie[current].items():
                    # Добавляем узел в очередь
                    queue.append(node)
                    
                    # Находим функцию неудачи для текущего узла
                    failure = self.fail[current]
                    
                    while failure != 0 and (failure not in self.trie or char not in self.trie[failure]):
                        failure = self.fail[failure]
                    
                    if failure in self.trie and char in self.trie[failure]:
                        failure = self.trie[failure][char]
                    
                    self.fail[node] = failure
                    
                    # Обновляем выходную функцию
                    if node not in self.output:
                        self.output[node] = []
                    
                    if failure in self.output:
                        self.output[node].extend(self.output[failure])
    
    def search(self, text):
        """
        Ищет все вхождения всех образцов в тексте.
        
        Args:
            text: Строка для поиска
            
        Returns:
            Словарь с результатами поиска для каждого образца
        """
        if not self.trie:
            return {}
        
        # Строим функцию неудачи, если это не было сделано ранее
        if not self.fail:
            self.build_failure_function()
        
        results = {}
        for pattern, index in self.patterns:
            results[index] = []
        
        # Поиск образцов в тексте
        state = 0
        for i, char in enumerate(text):
            # Находим следующее состояние
            while state != 0 and (state not in self.trie or char not in self.trie[state]):
                state = self.fail[state]
            
            if state in self.trie and char in self.trie[state]:
                state = self.trie[state][char]
            
            # Проверяем, есть ли совпадения для текущего состояния
            if state in self.output:
                for pattern_index in self.output[state]:
                    pattern, index = self.patterns[pattern_index]
                    results[index].append(i - len(pattern) + 1)
        
        return results

# Пример использования
text = "ABABDABACDABABCABAB"
patterns = ["ABABC", "AB", "BAB"]

ac = AhoCorasick()
for i, pattern in enumerate(patterns):
    ac.add_pattern(pattern, i)

search_results = ac.search(text)

for i, pattern in enumerate(patterns):
    positions = search_results[i]
    print(f"Образец '{pattern}' найден в позициях: {positions}")
```

**Сложность алгоритма Ахо-Корасик**:
- Временная сложность: O(n + m + z), где n — длина текста, m — суммарная длина всех образцов, z — количество найденных вхождений
- Пространственная сложность: O(m)

Алгоритм Ахо-Корасик оптимален для одновременного поиска множества образцов в тексте и широко используется в системах обнаружения вторжений, антивирусных программах и анализе биологических последовательностей.

## Регулярные выражения

Регулярные выражения (regex, regexp) — это мощный инструмент для описания шаблонов в строках. Они позволяют искать, проверять соответствие и манипулировать текстом с использованием компактных и гибких шаблонов.

### 1. Основы регулярных выражений

Регулярные выражения используют специальные символы и конструкции для определения шаблонов:

- `.` — любой символ, кроме перевода строки
- `^` — начало строки
- `$` — конец строки
- `*` — ноль или более повторений предыдущего элемента
- `+` — одно или более повторений предыдущего элемента
- `?` — ноль или одно повторение предыдущего элемента
- `{n}` — ровно n повторений предыдущего элемента
- `{n,}` — не менее n повторений предыдущего элемента
- `{n,m}` — от n до m повторений предыдущего элемента
- `[]` — один из символов в скобках
- `[^]` — любой символ, кроме указанных в скобках
- `|` — альтернатива (ИЛИ)
- `()` — группировка
- `\d` — цифра (эквивалентно `[0-9]`)
- `\w` — буква, цифра или подчеркивание (эквивалентно `[a-zA-Z0-9_]`)
- `\s` — пробельный символ
- `\b` — граница слова

### 2. Использование регулярных выражений в Python

Python предоставляет модуль `re` для работы с регулярными выражениями:

```python
import re

def regex_examples():
    """
    Демонстрация основных возможностей регулярных выражений в Python.
    """
    text = "The quick brown fox jumps over the lazy dog. The dog sleeps. Email: example@example.com"
    
    # 1. Поиск подстроки
    pattern = r"fox"
    match = re.search(pattern, text)
    print(f"1. Поиск 'fox': найдено на позиции {match.start() if match else 'не найдено'}")
    
    # 2. Поиск всех вхождений
    pattern = r"the"
    matches = re.findall(pattern, text, re.IGNORECASE)  # Игнорируем регистр
    print(f"2. Поиск 'the' (без учета регистра): {matches}")
    
    # 3. Использование метасимволов
    pattern = r"\b\w{4}\b"  # Слова из 4 букв
    matches = re.findall(pattern, text)
    print(f"3. Слова из 4 букв: {matches}")
    
    # 4. Группы и альтернативы
    pattern = r"(fox|dog)"
    matches = re.findall(pattern, text)
    print(f"4. 'fox' или 'dog': {matches}")
    
    # 5. Жадные и ленивые квантификаторы
    pattern_greedy = r"<.*>"
    pattern_lazy = r"<.*?>"
    text_html = "<p>Paragraph 1</p><p>Paragraph 2</p>"
    
    matches_greedy = re.findall(pattern_greedy, text_html)
    matches_lazy = re.findall(pattern_lazy, text_html)
    
    print(f"5a. Жадный поиск: {matches_greedy}")
    print(f"5b. Ленивый поиск: {matches_lazy}")
    
    # 6. Поиск и замена
    pattern = r"\b(\w+)(\s+)(\w+)\b"
    replacement = r"\3\2\1"  # Меняем слова местами
    result = re.sub(pattern, replacement, text)
    print(f"6. Замена: {result}")
    
    # 7. Валидация email
    email_pattern = r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
    test_emails = ["valid@example.com", "invalid@", "another.valid@sub.domain.com"]
    
    for email in test_emails:
        is_valid = bool(re.match(email_pattern, email))
        print(f"7. Email '{email}' {'валиден' if is_valid else 'невалиден'}")

# Запускаем примеры
regex_examples()
```

### 3. Конечные автоматы и регулярные выражения

Регулярные выражения тесно связаны с теорией автоматов. Они могут быть представлены как конечные автоматы, что делает их подходящими для эффективной реализации.

```python
class SimpleAutomaton:
    """
    Простая реализация детерминированного конечного автомата для регулярных выражений.
    Поддерживает только базовые операции: конкатенация, повторение (*) и альтернатива (|).
    """
    def __init__(self):
        self.states = {}
        self.transitions = {}
        self.initial_state = 0
        self.final_states = set()
        self.next_state = 1
    
    def add_transition(self, from_state, char, to_state):
        """
        Добавляет переход между состояниями.
        
        Args:
            from_state: Начальное состояние
            char: Символ перехода
            to_state: Конечное состояние
        """
        if from_state not in self.transitions:
            self.transitions[from_state] = {}
        self.transitions[from_state][char] = to_state
    
    def add_final_state(self, state):
        """
        Помечает состояние как конечное.
        
        Args:
            state: Состояние для пометки
        """
        self.final_states.add(state)
    
    def build_for_literal(self, literal):
        """
        Строит автомат для литерала (простой строки).
        
        Args:
            literal: Строка
            
        Returns:
            Кортеж (начальное состояние, конечное состояние)
        """
        start_state = self.next_state
        self.next_state += 1
        current_state = start_state
        
        for char in literal:
            next_state = self.next_state
            self.next_state += 1
            self.add_transition(current_state, char, next_state)
            current_state = next_state
        
        return start_state, current_state
    
    def match(self, text):
        """
        Проверяет, соответствует ли текст автомату.
        
        Args:
            text: Строка для проверки
            
        Returns:
            True, если текст соответствует автомату, иначе False
        """
        current_state = self.initial_state
        
        for char in text:
            if (current_state in self.transitions and 
                char in self.transitions[current_state]):
                current_state = self.transitions[current_state][char]
            else:
                return False
        
        return current_state in self.final_states

# Пример использования
def build_simple_pattern_automaton(pattern):
    """
    Строит простой автомат для базового шаблона.
    
    Args:
        pattern: Строковый шаблон
        
    Returns:
        Автомат для шаблона
    """
    automaton = SimpleAutomaton()
    
    start, end = automaton.build_for_literal(pattern)
    automaton.initial_state = start
    automaton.add_final_state(end)
    
    return automaton

# Создаем простой автомат для шаблона "abc"
pattern = "abc"
automaton = build_simple_pattern_automaton(pattern)

# Проверяем соответствие строк
test_strings = ["abc", "abcd", "ab", "xabc"]
for s in test_strings:
    result = automaton.match(s)
    print(f"Строка '{s}' {'соответствует' if result else 'не соответствует'} шаблону '{pattern}'")
```

### 4. Построение регулярных выражений для практических задач

```python
def regex_practical_examples():
    """
    Практические примеры использования регулярных выражений.
    """
    import re
    
    # Пример 1: Проверка правильности номера телефона
    phone_pattern = r"^\+?(\d{1,3})[-. ]?(\d{3})[-. ]?(\d{3})[-. ]?(\d{4})$"
    test_phones = ["+1 123-456-7890", "123.456.7890", "+7 (123) 456-78-90", "1234567890", "123-456-78"]
    
    print("1. Проверка номеров телефонов:")
    for phone in test_phones:
        is_valid = bool(re.match(phone_pattern, phone))
        print(f"   Номер '{phone}' {'валиден' if is_valid else 'невалиден'}")
    
    # Пример 2: Извлечение URL из текста
    url_pattern = r"https?://(?:[-\w.]|(?:%[\da-fA-F]{2}))+"
    text_with_urls = "Visit our website at http://example.com or https://www.example.org/page"
    
    urls = re.findall(url_pattern, text_with_urls)
    print("\n2. Извлечение URL из текста:")
    print(f"   Найденные URL: {urls}")
    
    # Пример 3: Парсинг данных из структурированного текста
    log_entry = '127.0.0.1 - - [21/Oct/2023:12:34:56 -0500] "GET /index.html HTTP/1.1" 200 1234'
    log_pattern = r'(\d+\.\d+\.\d+\.\d+).*\[(\d+/\w+/\d+:\d+:\d+:\d+).*\] "(\w+) ([^"]*)" (\d+) (\d+)'
    
    match = re.match(log_pattern, log_entry)
    if match:
        ip, date, method, path, status, size = match.groups()
        print("\n3. Парсинг лог-файла:")
        print(f"   IP: {ip}")
        print(f"   Дата: {date}")
        print(f"   Метод: {method}")
        print(f"   Путь: {path}")
        print(f"   Статус: {status}")
        print(f"   Размер: {size}")
    
    # Пример 4: Валидация пароля
    password_pattern = r"^(?=.*[A-Z])(?=.*[a-z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$"
    test_passwords = ["Weak12!", "nouppercaseП1!", "NoDigits!", "NoSpecialChars123", "Short1!"]
    
    print("\n4. Проверка сложности пароля:")
    for password in test_passwords:
        is_valid = bool(re.match(password_pattern, password))
        print(f"   Пароль '{password}' {'достаточно сложный' if is_valid else 'слишком простой'}")
    
    # Пример 5: Извлечение названий глав из текста
    text_with_chapters = """
    Chapter 1: Introduction
    Some content here.
    
    Chapter 2: Background
    More content.
    
    Chapter 3: Methods
    """
    
    chapter_pattern = r"Chapter\s+(\d+):\s+(.+)$"
    chapters = re.findall(chapter_pattern, text_with_chapters, re.MULTILINE)
    
    print("\n5. Извлечение глав из текста:")
    for num, title in chapters:
        print(f"   Глава {num}: {title}")

# Запускаем практические примеры
regex_practical_examples()
```

### 5. Реализация простого движка регулярных выражений

Рассмотрим реализацию базового движка регулярных выражений, который поддерживает операторы конкатенации, альтернативы и повторения:

```python
class RegExEngine:
    """
    Простой движок регулярных выражений, поддерживающий базовые операции:
    - Литералы (a, b, c...)
    - Конкатенация (ab)
    - Альтернатива (a|b)
    - Повторение (a*)
    - Группировка ((a|b)c)
    """
    def __init__(self, pattern):
        self.pattern = pattern
        self.pos = 0
    
    def match(self, text):
        """
        Проверяет, соответствует ли текст шаблону.
        
        Args:
            text: Строка для проверки
            
        Returns:
            True, если текст соответствует шаблону, иначе False
        """
        # Сбрасываем позицию
        self.pos = 0
        # Пытаемся сопоставить весь шаблон с началом текста
        matched, length = self._match_pattern(self.pattern, text, 0)
        # Возвращаем True, только если сопоставили весь текст
        return matched and length == len(text)
    
    def search(self, text):
        """
        Ищет первое вхождение шаблона в тексте.
        
        Args:
            text: Строка для поиска
            
        Returns:
            Индекс первого вхождения или -1, если шаблон не найден
        """
        for i in range(len(text)):
            self.pos = 0
            matched, _ = self._match_pattern(self.pattern, text, i)
            if matched:
                return i
        return -1
    
    def _match_pattern(self, pattern, text, start_idx):
        """
        Сопоставляет шаблон с текстом, начиная с указанной позиции.
        
        Args:
            pattern: Шаблон для сопоставления
            text: Исходный текст
            start_idx: Индекс начала сопоставления в тексте
            
        Returns:
            Кортеж (успех, длина_совпадения)
        """
        i = start_idx  # Позиция в тексте
        j = 0          # Позиция в шаблоне
        
        while j < len(pattern):
            # Если достигли конца текста, но не конца шаблона
            if i >= len(text):
                return False, 0
            
            char = pattern[j]
            
            # Обработка специальных символов
            if char == '\\':  # Экранирование
                j += 1
                if j >= len(pattern):
                    return False, 0
                if pattern[j] != text[i]:
                    return False, 0
                i += 1
                j += 1
            
            elif char == '.':  # Любой символ
                i += 1
                j += 1
            
            elif char == '*':  # Повторение предыдущего элемента
                if j == 0:
                    return False, 0
                
                prev_char = pattern[j-1]
                # Пытаемся сопоставить как можно больше
                while i < len(text) and text[i] == prev_char:
                    i += 1
                
                j += 1
            
            elif char == '|':  # Альтернатива
                # Пытаемся сопоставить левую часть
                left_pattern = pattern[:j]
                matched_left, left_len = self._match_pattern(left_pattern, text, start_idx)
                
                if matched_left:
                    return True, left_len
                
                # Пытаемся сопоставить правую часть
                right_pattern = pattern[j+1:]
                matched_right, right_len = self._match_pattern(right_pattern, text, start_idx)
                
                return matched_right, right_len
            
            else:  # Обычный символ
                if char != text[i]:
                    return False, 0
                i += 1
                j += 1
        
        return True, i - start_idx

# Пример использования
regex_engine = RegExEngine("a*b|cd")

test_strings = ["b", "ab", "aab", "cd", "ac", "aabd"]
for s in test_strings:
    result = regex_engine.match(s)
    print(f"Строка '{s}' {'соответствует' if result else 'не соответствует'} шаблону '{regex_engine.pattern}'")

for s in test_strings:
    index = regex_engine.search(s)
    if index >= 0:
        print(f"Шаблон '{regex_engine.pattern}' найден в строке '{s}' на позиции {index}")
    else:
        print(f"Шаблон '{regex_engine.pattern}' не найден в строке '{s}'")
```

## Расстояние Левенштейна (редакционное расстояние)

Расстояние Левенштейна, или редакционное расстояние, — это мера разницы между двумя строками, определяемая как минимальное количество операций редактирования (вставки, удаления и замены), необходимых для преобразования одной строки в другую.

### 1. Базовая реализация алгоритма

```python
def levenshtein_distance(s1, s2):
    """
    Вычисляет расстояние Левенштейна между двумя строками.
    
    Args:
        s1: Первая строка
        s2: Вторая строка
        
    Returns:
        Целое число - редакционное расстояние
    """
    # Создаем матрицу размера (len(s1) + 1) x (len(s2) + 1)
    dp = [[0 for _ in range(len(s2) + 1)] for _ in range(len(s1) + 1)]
    
    # Инициализируем первую строку и первый столбец
    for i in range(len(s1) + 1):
        dp[i][0] = i  # Стоимость удаления i символов из s1
    
    for j in range(len(s2) + 1):
        dp[0][j] = j  # Стоимость вставки j символов из s2
    
    # Заполняем матрицу
    for i in range(1, len(s1) + 1):
        for j in range(1, len(s2) + 1):
            # Если символы совпадают, никаких операций не требуется
            if s1[i - 1] == s2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                # Находим минимум из трех операций:
                # 1. Замена символа: dp[i-1][j-1] + 1
                # 2. Удаление символа: dp[i-1][j] + 1
                # 3. Вставка символа: dp[i][j-1] + 1
                dp[i][j] = 1 + min(dp[i - 1][j - 1], dp[i - 1][j], dp[i][j - 1])
    
    # Последний элемент матрицы содержит искомое расстояние
    return dp[len(s1)][len(s2)]

# Пример использования
def test_levenshtein():
    """
    Тестирует функцию расчета расстояния Левенштейна на разных примерах.
    """
    test_cases = [
        ("kitten", "sitting", 3),
        ("saturday", "sunday", 3),
        ("abc", "abc", 0),
        ("", "abc", 3),
        ("abc", "", 3),
        ("intention", "execution", 5)
    ]
    
    for s1, s2, expected in test_cases:
        distance = levenshtein_distance(s1, s2)
        print(f"Расстояние между '{s1}' и '{s2}': {distance} (ожидалось: {expected})")

test_levenshtein()
```

### 2. Оптимизированная версия алгоритма

```python
def levenshtein_distance_optimized(s1, s2):
    """
    Оптимизированная по памяти реализация расстояния Левенштейна.
    Использует только две строки матрицы вместо полной матрицы.
    
    Args:
        s1: Первая строка
        s2: Вторая строка
        
    Returns:
        Целое число - редакционное расстояние
    """
    # Оптимизация: если одна из строк пуста, расстояние равно длине другой
    if len(s1) == 0:
        return len(s2)
    if len(s2) == 0:
        return len(s1)
    
    # Оптимизация: гарантируем, что s1 - более короткая строка
    if len(s1) > len(s2):
        s1, s2 = s2, s1
    
    # Используем только две строки матрицы
    previous_row = list(range(len(s2) + 1))
    current_row = [0] * (len(s2) + 1)
    
    for i in range(1, len(s1) + 1):
        current_row[0] = i
        
        for j in range(1, len(s2) + 1):
            # Вычисляем текущее значение из предыдущих
            if s1[i - 1] == s2[j - 1]:
                current_row[j] = previous_row[j - 1]
            else:
                current_row[j] = 1 + min(previous_row[j - 1], previous_row[j], current_row[j - 1])
        
        # Переключаем строки
        previous_row, current_row = current_row, previous_row
    
    return previous_row[len(s2)]

# Пример использования
print("\nОптимизированная версия:")
test_cases = [
    ("kitten", "sitting", 3),
    ("saturday", "sunday", 3),
    ("abc", "abc", 0),
    ("", "abc", 3),
    ("abc", "", 3),
    ("intention", "execution", 5)
]

for s1, s2, expected in test_cases:
    distance = levenshtein_distance_optimized(s1, s2)
    print(f"Расстояние между '{s1}' и '{s2}': {distance} (ожидалось: {expected})")
```

### 3. Расстояние Дамерау-Левенштейна

Расстояние Дамерау-Левенштейна — это вариант расстояния Левенштейна, который также учитывает операцию транспозиции (перестановки двух соседних символов).

```python
def damerau_levenshtein_distance(s1, s2):
    """
    Вычисляет расстояние Дамерау-Левенштейна между двумя строками.
    
    Args:
        s1: Первая строка
        s2: Вторая строка
        
    Returns:
        Целое число - редакционное расстояние с учетом транспозиций
    """
    # Создаем матрицу размера (len(s1) + 1) x (len(s2) + 1)
    dp = [[0 for _ in range(len(s2) + 1)] for _ in range(len(s1) + 1)]
    
    # Инициализируем первую строку и первый столбец
    for i in range(len(s1) + 1):
        dp[i][0] = i
    
    for j in range(len(s2) + 1):
        dp[0][j] = j
    
    # Заполняем матрицу
    for i in range(1, len(s1) + 1):
        for j in range(1, len(s2) + 1):
            # Определяем стоимость операций
            cost = 0 if s1[i - 1] == s2[j - 1] else 1
            
            # Обычные операции Левенштейна
            dp[i][j] = min(
                dp[i - 1][j] + 1,         # Удаление
                dp[i][j - 1] + 1,         # Вставка
                dp[i - 1][j - 1] + cost   # Замена или совпадение
            )
            
            # Проверяем возможность транспозиции
            if (i > 1 and j > 1 and 
                s1[i - 1] == s2[j - 2] and 
                s1[i - 2] == s2[j - 1]):
                dp[i][j] = min(dp[i][j], dp[i - 2][j - 2] + cost)
    
    return dp[len(s1)][len(s2)]

# Пример использования
print("\nРасстояние Дамерау-Левенштейна:")
test_cases = [
    ("kitten", "sitting", 3),
    ("saturday", "sunday", 3),
    ("abc", "abc", 0),
    ("", "abc", 3),
    ("abc", "", 3),
    ("ca", "abc", 2),
    ("abc", "acb", 1)  # Транспозиция 'b' и 'c'
]

for s1, s2, expected in test_cases:
    distance = damerau_levenshtein_distance(s1, s2)
    print(f"Расстояние между '{s1}' и '{s2}': {distance} (ожидалось: {expected})")
```

### 4. Восстановление операций редактирования

Можно не только вычислить расстояние Левенштейна, но и восстановить последовательность операций редактирования:

```python
def levenshtein_distance_with_operations(s1, s2):
    """
    Вычисляет расстояние Левенштейна и возвращает последовательность операций редактирования.
    
    Args:
        s1: Первая строка
        s2: Вторая строка
        
    Returns:
        Кортеж (расстояние, список операций)
    """
    # Создаем матрицу размера (len(s1) + 1) x (len(s2) + 1)
    dp = [[0 for _ in range(len(s2) + 1)] for _ in range(len(s1) + 1)]
    
    # Инициализируем первую строку и первый столбец
    for i in range(len(s1) + 1):
        dp[i][0] = i
    
    for j in range(len(s2) + 1):
        dp[0][j] = j
    
    # Заполняем матрицу
    for i in range(1, len(s1) + 1):
        for j in range(1, len(s2) + 1):
            if s1[i - 1] == s2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                dp[i][j] = 1 + min(dp[i - 1][j - 1], dp[i - 1][j], dp[i][j - 1])
    
    # Восстанавливаем операции редактирования
    operations = []
    i, j = len(s1), len(s2)
    
    while i > 0 or j > 0:
        if i > 0 and j > 0 and s1[i - 1] == s2[j - 1]:
            # Символы совпадают, операция не нужна
            i -= 1
            j -= 1
        elif i > 0 and j > 0 and dp[i][j] == dp[i - 1][j - 1] + 1:
            # Замена символа
            operations.append(("replace", i - 1, s1[i - 1], s2[j - 1]))
            i -= 1
            j -= 1
        elif i > 0 and dp[i][j] == dp[i - 1][j] + 1:
            # Удаление символа
            operations.append(("delete", i - 1, s1[i - 1]))
            i -= 1
        else:  # j > 0 and dp[i][j] == dp[i][j - 1] + 1
            # Вставка символа
            operations.append(("insert", i, s2[j - 1]))
            j -= 1
    
    # Переворачиваем список, чтобы операции шли в правильном порядке
    operations.reverse()
    
    return dp[len(s1)][len(s2)], operations

# Пример использования
print("\nОперации редактирования:")
test_cases = [
    ("kitten", "sitting"),
    ("algorithm", "altruistic"),
    ("abc", "adc")
]

for s1, s2 in test_cases:
    distance, operations = levenshtein_distance_with_operations(s1, s2)
    print(f"Преобразование '{s1}' в '{s2}' (расстояние: {distance}):")
    for op in operations:
        if op[0] == "replace":
            print(f"  Заменить символ '{op[2]}' на позиции {op[1]} на '{op[3]}'")
        elif op[0] == "delete":
            print(f"  Удалить символ '{op[2]}' на позиции {op[1]}")
        elif op[0] == "insert":
            print(f"  Вставить символ '{op[2]}' на позицию {op[1]}")
```

### 5. Применение расстояния Левенштейна

#### Проверка орфографии и предложение исправлений

```python
def suggest_corrections(word, dictionary, max_distance=2):
    """
    Предлагает исправления для слова на основе словаря и расстояния Левенштейна.
    
    Args:
        word: Слово для проверки
        dictionary: Список или множество корректных слов
        max_distance: Максимальное допустимое расстояние Левенштейна
        
    Returns:
        Список предлагаемых исправлений, отсортированный по расстоянию
    """
    suggestions = []
    
    for dict_word in dictionary:
        distance = levenshtein_distance_optimized(word, dict_word)
        if distance <= max_distance:
            suggestions.append((dict_word, distance))
    
    # Сортируем по расстоянию (наиболее вероятные исправления сначала)
    suggestions.sort(key=lambda x: x[1])
    
    return suggestions

# Пример использования
print("\nПроверка орфографии:")
dictionary = ["apple", "banana", "orange", "pear", "grape", "lemon", "application", "apply", "append"]
misspelled_words = ["appel", "bannana", "orage", "appl"]

for word in misspelled_words:
    corrections = suggest_corrections(word, dictionary)
    if corrections:
        suggestions = ", ".join([f"{w} (расстояние: {d})" for w, d in corrections])
        print(f"Слово '{word}': возможные исправления: {suggestions}")
    else:
        print(f"Слово '{word}': исправлений не найдено")
```

#### Сравнение строк и нечеткий поиск

```python
def fuzzy_search(query, texts, threshold=0.7):
    """
    Выполняет нечеткий поиск запроса в списке текстов.
    
    Args:
        query: Строка запроса
        texts: Список текстов для поиска
        threshold: Порог схожести (0.0 до 1.0)
        
    Returns:
        Список кортежей (текст, степень схожести) для подходящих текстов
    """
    results = []
    
    for text in texts:
        # Находим наименьшее расстояние между запросом и подстрокой текста
        min_distance = float('inf')
        for i in range(len(text) - len(query) + 1):
            substring = text[i:i + len(query)]
            distance = levenshtein_distance_optimized(query, substring)
            min_distance = min(min_distance, distance)
        
        # Вычисляем степень схожести (1.0 - полное совпадение)
        similarity = 1.0 - min_distance / max(len(query), 1)
        
        if similarity >= threshold:
            results.append((text, similarity))
    
    # Сортируем по убыванию схожести
    results.sort(key=lambda x: x[1], reverse=True)
    
    return results

# Пример использования
print("\nНечеткий поиск:")
texts = [
    "The quick brown fox jumps over the lazy dog",
    "A brown fox quickly jumped over a lazy dog",
    "The lazy cat sleeps all day",
    "Lorem ipsum dolor sit amet",
    "Quick brown foxes are common in stories"
]

queries = ["brown fox", "quik brwn", "lazy", "ipsum"]

for query in queries:
    results = fuzzy_search(query, texts)
    print(f"Запрос: '{query}'")
    for text, similarity in results:
        print(f"  {similarity:.2f}: {text}")
```

#### Биоинформатика: сравнение последовательностей ДНК

```python
def align_sequences(seq1, seq2):
    """
    Выполняет попарное выравнивание двух биологических последовательностей
    на основе расстояния Левенштейна.
    
    Args:
        seq1: Первая последовательность
        seq2: Вторая последовательность
        
    Returns:
        Кортеж (расстояние, выровненная_последовательность_1, выровненная_последовательность_2)
    """
    # Создаем матрицу для расстояния Левенштейна и трассировки
    dp = [[0 for _ in range(len(seq2) + 1)] for _ in range(len(seq1) + 1)]
    
    # Инициализируем первую строку и первый столбец
    for i in range(len(seq1) + 1):
        dp[i][0] = i
    
    for j in range(len(seq2) + 1):
        dp[0][j] = j
    
    # Заполняем матрицу
    for i in range(1, len(seq1) + 1):
        for j in range(1, len(seq2) + 1):
            # Определяем стоимость операций
            # Совпадение: 0, Несовпадение: 1, Пробел: 1
            match_cost = 0 if seq1[i - 1] == seq2[j - 1] else 1
            
            dp[i][j] = min(
                dp[i - 1][j] + 1,          # Делеция (пробел в seq2)
                dp[i][j - 1] + 1,          # Вставка (пробел в seq1)
                dp[i - 1][j - 1] + match_cost  # Совпадение или замена
            )
    
    # Восстанавливаем выравнивание
    aligned_seq1 = []
    aligned_seq2 = []
    i, j = len(seq1), len(seq2)
    
    while i > 0 or j > 0:
        if i > 0 and j > 0 and dp[i][j] == dp[i - 1][j - 1] + (0 if seq1[i - 1] == seq2[j - 1] else 1):
            # Совпадение или замена
            aligned_seq1.append(seq1[i - 1])
            aligned_seq2.append(seq2[j - 1])
            i -= 1
            j -= 1
        elif i > 0 and dp[i][j] == dp[i - 1][j] + 1:
            # Делеция (пробел в seq2)
            aligned_seq1.append(seq1[i - 1])
            aligned_seq2.append("-")
            i -= 1
        else:  # j > 0 and dp[i][j] == dp[i][j - 1] + 1
            # Вставка (пробел в seq1)
            aligned_seq1.append("-")
            aligned_seq2.append(seq2[j - 1])
            j -= 1
    
    # Переворачиваем последовательности
    aligned_seq1.reverse()
    aligned_seq2.reverse()
    
    # Преобразуем список символов обратно в строки
    aligned_seq1_str = ''.join(aligned_seq1)
    aligned_seq2_str = ''.join(aligned_seq2)
    
    return dp[len(seq1)][len(seq2)], aligned_seq1_str, aligned_seq2_str

# Пример использования
print("\nВыравнивание биологических последовательностей:")
dna_sequences = [
    ("ACGTACGT", "ACGACGT"),
    ("ATCGTACGTA", "ATCGTA"),
    ("GATTACA", "GATACA")
]

for seq1, seq2 in dna_sequences:
    distance, aligned1, aligned2 = align_sequences(seq1, seq2)
    print(f"Последовательности: {seq1}, {seq2}")
    print(f"Расстояние: {distance}")
    print(f"Выравнивание:")
    print(f"  {aligned1}")
    print(f"  {aligned2}")
    print(f"  {''.join(['|' if a == b and a != '-' else ' ' for a, b in zip(aligned1, aligned2)])}")
```

## Практические применения строковых алгоритмов

### 1. Индексация и поиск текста

```python
class SimpleTextIndex:
    """
    Простой индекс для быстрого поиска текста с использованием инвертированного индекса.
    """
    def __init__(self):
        self.documents = {}  # Словарь документов: id -> текст
        self.index = {}      # Инвертированный индекс: слово -> {id1, id2, ...}
        self.doc_count = 0   # Счетчик документов
    
    def add_document(self, text, doc_id=None):
        """
        Добавляет документ в индекс.
        
        Args:
            text: Текст документа
            doc_id: Идентификатор документа (если None, генерируется автоматически)
            
        Returns:
            Идентификатор добавленного документа
        """
        if doc_id is None:
            doc_id = self.doc_count
            self.doc_count += 1
        
        # Сохраняем документ
        self.documents[doc_id] = text
        
        # Разбиваем текст на слова и добавляем в индекс
        words = text.lower().split()
        for word in words:
            # Удаляем знаки препинания
            word = word.strip('.,;:!?"\'()[]{}')
            if word:
                if word not in self.index:
                    self.index[word] = set()
                self.index[word].add(doc_id)
        
        return doc_id
    
    def search(self, query):
        """
        Ищет документы, содержащие все слова запроса.
        
        Args:
            query: Строка запроса
            
        Returns:
            Список идентификаторов подходящих документов
        """
        query_words = query.lower().split()
        query_words = [word.strip('.,;:!?"\'()[]{}') for word in query_words if word.strip('.,;:!?"\'()[]{}')]
        
        if not query_words:
            return []
        
        # Начинаем с документов, содержащих первое слово
        result_set = self.index.get(query_words[0], set())
        
        # Пересекаем с документами, содержащими остальные слова
        for word in query_words[1:]:
            doc_set = self.index.get(word, set())
            result_set &= doc_set  # Пересечение множеств
        
        return list(result_set)
    
    def fuzzy_search(self, query, max_distance=1):
        """
        Выполняет нечеткий поиск с использованием расстояния Левенштейна.
        
        Args:
            query: Строка запроса
            max_distance: Максимальное допустимое расстояние Левенштейна
            
        Returns:
            Список идентификаторов подходящих документов
        """
        query_words = query.lower().split()
        query_words = [word.strip('.,;:!?"\'()[]{}') for word in query_words if word.strip('.,;:!?"\'()[]{}')]
        
        if not query_words:
            return []
        
        result_set = set()
        processed_words = set()
        
        for word in query_words:
            # Ищем похожие слова в индексе
            similar_words = []
            for index_word in self.index:
                if index_word in processed_words:
                    continue
                
                distance = levenshtein_distance_optimized(word, index_word)
                if distance <= max_distance:
                    similar_words.append((index_word, distance))
                    processed_words.add(index_word)
            
            # Сортируем по расстоянию и берем самые похожие
            similar_words.sort(key=lambda x: x[1])
            
            # Объединяем документы для похожих слов
            for similar_word, _ in similar_words[:3]:  # Берем до 3 похожих слов
                result_set |= self.index.get(similar_word, set())
        
        return list(result_set)
    
    def get_document(self, doc_id):
        """
        Возвращает текст документа по идентификатору.
        
        Args:
            doc_id: Идентификатор документа
            
        Returns:
            Текст документа или None, если документ не найден
        """
        return self.documents.get(doc_id)

# Пример использования
print("\nИндексация и поиск текста:")
index = SimpleTextIndex()

# Добавляем документы
docs = [
    "The quick brown fox jumps over the lazy dog",
    "A brown fox quickly jumped over a lazy dog",
    "The lazy cat sleeps all day",
    "Lorem ipsum dolor sit amet, consectetur adipiscing elit",
    "Python is a popular programming language for text processing",
    "Text processing algorithms are important in information retrieval"
]

for doc in docs:
    index.add_document(doc)

# Точный поиск
queries = ["brown fox", "lazy", "processing", "nonexistent"]
for query in queries:
    results = index.search(query)
    print(f"Поиск: '{query}'")
    for doc_id in results:
        print(f"  Документ {doc_id}: {index.get_document(doc_id)}")

# Нечеткий поиск
fuzzy_queries = ["brwn fx", "lasy", "procesing"]
for query in fuzzy_queries:
    results = index.fuzzy_search(query)
    print(f"Нечеткий поиск: '{query}'")
    for doc_id in results:
        print(f"  Документ {doc_id}: {index.get_document(doc_id)}")
```

### 2. Автоматическое дополнение (автокомплит)

```python
class Trie:
    """
    Реализация префиксного дерева (бора) для автоматического дополнения.
    """
    def __init__(self):
        self.root = {}
        self.end_symbol = "*"
    
    def insert(self, word, weight=1):
        """
        Добавляет слово в префиксное дерево с указанным весом.
        
        Args:
            word: Слово для добавления
            weight: Вес слова (для ранжирования предложений)
        """
        node = self.root
        for char in word:
            if char not in node:
                node[char] = {}
            node = node[char]
        
        # Помечаем конец слова и сохраняем вес
        node[self.end_symbol] = weight
    
    def search(self, word):
        """
        Проверяет, существует ли слово в префиксном дереве.
        
        Args:
            word: Слово для поиска
            
        Returns:
            True, если слово существует, иначе False
        """
        node = self.root
        for char in word:
            if char not in node:
                return False
            node = node[char]
        
        return self.end_symbol in node
    
    def starts_with(self, prefix):
        """
        Проверяет, существуют ли слова, начинающиеся с заданного префикса.
        
        Args:
            prefix: Префикс для проверки
            
        Returns:
            True, если есть слова с таким префиксом, иначе False
        """
        node = self.root
        for char in prefix:
            if char not in node:
                return False
            node = node[char]
        
        return True
    
    def get_node(self, prefix):
        """
        Возвращает узел, соответствующий заданному префиксу.
        
        Args:
            prefix: Префикс для поиска
            
        Returns:
            Узел префиксного дерева или None, если префикс не найден
        """
        node = self.root
        for char in prefix:
            if char not in node:
                return None
            node = node[char]
        
        return node
    
    def _get_all_words(self, node, prefix, words):
        """
        Рекурсивно собирает все слова с заданным префиксом.
        
        Args:
            node: Текущий узел
            prefix: Текущий префикс
            words: Список для хранения слов с их весами
        """
        if self.end_symbol in node:
            words.append((prefix, node[self.end_symbol]))
        
        for char in node:
            if char != self.end_symbol:
                self._get_all_words(node[char], prefix + char, words)
    
    def autocomplete(self, prefix, max_suggestions=5):
        """
        Предлагает слова для автодополнения по заданному префиксу.
        
        Args:
            prefix: Префикс для автодополнения
            max_suggestions: Максимальное количество предложений
            
        Returns:
            Список слов, начинающихся с заданного префикса, отсортированный по весу
        """
        node = self.get_node(prefix)
        if not node:
            return []
        
        words = []
        self._get_all_words(node, prefix, words)
        
        # Сортируем по весу (от большего к меньшему)
        words.sort(key=lambda x: x[1], reverse=True)
        
        # Возвращаем только слова, ограничивая количество
        return [word for word, _ in words[:max_suggestions]]
    
    def fuzzy_autocomplete(self, prefix, max_distance=1, max_suggestions=5):
        """
        Предлагает слова для нечеткого автодополнения, допуская ошибки в префиксе.
        
        Args:
            prefix: Префикс для автодополнения
            max_distance: Максимальное допустимое расстояние Левенштейна
            max_suggestions: Максимальное количество предложений
            
        Returns:
            Список слов, начинающихся с похожих префиксов, отсортированный по весу и расстоянию
        """
        if not prefix:
            return self.autocomplete("", max_suggestions)
        
        # Собираем все слова из префиксного дерева
        all_words = []
        self._get_all_words(self.root, "", all_words)
        
        # Фильтруем и ранжируем слова
        results = []
        for word, weight in all_words:
            if len(word) >= len(prefix):
                # Проверяем расстояние между префиксом и началом слова
                distance = levenshtein_distance_optimized(prefix, word[:len(prefix)])
                if distance <= max_distance:
                    results.append((word, weight, distance))
        
        # Сортируем по расстоянию (возрастание) и весу (убывание)
        results.sort(key=lambda x: (x[2], -x[1]))
        
        # Возвращаем только слова, ограничивая количество
        return [word for word, _, _ in results[:max_suggestions]]

# Пример использования
print("\nАвтоматическое дополнение:")
autocomplete = Trie()

# Добавляем слова с весами (частотами)
words_with_weights = [
    ("apple", 10),
    ("application", 8),
    ("apply", 7),
    ("append", 5),
    ("algorithm", 9),
    ("binary", 6),
    ("bind", 4),
    ("python", 15),
    ("program", 12),
    ("programming", 14),
    ("code", 11),
    ("coding", 10)
]

for word, weight in words_with_weights:
    autocomplete.insert(word, weight)

# Обычное автодополнение
prefixes = ["a", "ap", "pro", "c", "z"]
for prefix in prefixes:
    suggestions = autocomplete.autocomplete(prefix)
    print(f"Автодополнение для '{prefix}': {suggestions}")

# Нечеткое автодополнение
fuzzy_prefixes = ["aple", "prgrm", "binry"]
for prefix in fuzzy_prefixes:
    suggestions = autocomplete.fuzzy_autocomplete(prefix)
    print(f"Нечеткое автодополнение для '{prefix}': {suggestions}")
```

### 3. Обработка естественного языка: токенизация и стемминг

```python
def simple_tokenize(text):
    """
    Простая токенизация текста.
    
    Args:
        text: Исходный текст
        
    Returns:
        Список токенов (слов)
    """
    import re
    
    # Преобразуем текст к нижнему регистру
    text = text.lower()
    
    # Заменяем все не-буквенные и не-цифровые символы на пробелы
    text = re.sub(r'[^\w\s]', ' ', text)
    
    # Разбиваем на токены
    tokens = text.split()
    
    return tokens

def simple_stemming(word):
    """
    Простой алгоритм стемминга для английских слов.
    Удаляет наиболее распространенные суффиксы.
    
    Args:
        word: Слово для стемминга
        
    Returns:
        Основа слова
    """
    # Удаляем суффиксы множественного числа
    if word.endswith('s') and len(word) > 3:
        word = word[:-1]
    
    # Удаляем суффиксы прошедшего времени и причастий
    if word.endswith('ed') and len(word) > 4:
        word = word[:-2]
    elif word.endswith('ing') and len(word) > 5:
        word = word[:-3]
    
    # Удаляем суффиксы прилагательных
    if word.endswith('ly') and len(word) > 4:
        word = word[:-2]
    
    return word

def text_preprocessing(text):
    """
    Выполняет предварительную обработку текста: токенизацию и стемминг.
    
    Args:
        text: Исходный текст
        
    Returns:
        Список обработанных токенов
    """
    tokens = simple_tokenize(text)
    stemmed_tokens = [simple_stemming(token) for token in tokens]
    
    return stemmed_tokens

# Пример использования
print("\nОбработка естественного языка:")
texts = [
    "The quick brown foxes are jumping over the lazy dogs",
    "She is programming a new application in Python",
    "Running and walking are healthy exercises"
]

for text in texts:
    tokens = simple_tokenize(text)
    stemmed_tokens = text_preprocessing(text)
    
    print(f"Исходный текст: {text}")
    print(f"Токенизация: {tokens}")
    print(f"После стемминга: {stemmed_tokens}")
```

### 4. Сжатие данных: алгоритм кодирования длин серий (RLE)

```python
def run_length_encode(text):
    """
    Выполняет сжатие текста с помощью алгоритма кодирования длин серий (RLE).
    
    Args:
        text: Исходный текст
        
    Returns:
        Сжатый текст
    """
    if not text:
        return ""
    
    encoded = []
    count = 1
    current_char = text[0]
    
    for i in range(1, len(text)):
        if text[i] == current_char:
            count += 1
        else:
            encoded.append(f"{count}{current_char}")
            current_char = text[i]
            count = 1
    
    # Добавляем последнюю серию
    encoded.append(f"{count}{current_char}")
    
    return "".join(encoded)

def run_length_decode(encoded_text):
    """
    Выполняет распаковку текста, сжатого алгоритмом RLE.
    
    Args:
        encoded_text: Сжатый текст
        
    Returns:
        Восстановленный текст
    """
    if not encoded_text:
        return ""
    
    decoded = []
    i = 0
    
    while i < len(encoded_text):
        # Извлекаем число повторений
        count_str = ""
        while i < len(encoded_text) and encoded_text[i].isdigit():
            count_str += encoded_text[i]
            i += 1
        
        # Извлекаем символ
        if i < len(encoded_text):
            char = encoded_text[i]
            i += 1
            
            # Восстанавливаем серию
            count = int(count_str)
            decoded.append(char * count)
    
    return "".join(decoded)

# Пример использования
print("\nСжатие данных (RLE):")
test_strings = [
    "AAAABBBCCDAA",
    "ABCDEFG",
    "AAAAAAAAAAAABBBBBBBBBBBBCCCCCCCCCCCCDDDDDDDDDDDD"
]

for text in test_strings:
    encoded = run_length_encode(text)
    decoded = run_length_decode(encoded)
    
    compression_ratio = len(encoded) / len(text) if len(text) > 0 else 0
    
    print(f"Исходный текст ({len(text)} символов): {text}")
    print(f"Сжатый текст ({len(encoded)} символов): {encoded}")
    print(f"Коэффициент сжатия: {compression_ratio:.2f}")
    print(f"Проверка: исходный текст {'совпадает' if text == decoded else 'не совпадает'} с распакованным")
```

## Заключение

Строковые алгоритмы — это важная область алгоритмов и структур данных, которая находит широкое применение в различных областях информатики: от поиска текста и автодополнения до обработки естественного языка и биоинформатики.

В этом разделе мы рассмотрели несколько ключевых строковых алгоритмов:

1. **Алгоритмы поиска подстроки** (наивный, Кнута-Морриса-Пратта, Бойера-Мура, Рабина-Карпа, Z-функция и Ахо-Корасик), которые позволяют эффективно находить вхождения образцов в тексте.

2. **Регулярные выражения** — мощный инструмент для описания и поиска шаблонов в тексте, включая их теоретическую основу (конечные автоматы) и практическое применение.

3. **Расстояние Левенштейна (редакционное расстояние)** — меру сходства между строками, которая находит применение в проверке орфографии, нечетком поиске и биоинформатике.

4. **Практические применения строковых алгоритмов**: индексация и поиск текста, автоматическое дополнение, обработка естественного языка и сжатие данных.

Эффективные строковые алгоритмы критически важны для многих современных приложений, особенно с ростом объема текстовых данных в интернете, социальных сетях и научных исследованиях. Понимание их принципов работы и умение применять их на практике — важный навык для любого программиста.

---

В следующем разделе мы рассмотрим графовые алгоритмы, включая алгоритмы поиска кратчайшего пути (Дейкстра, Беллман-Форд), алгоритмы построения минимального остовного дерева (Прима, Крускала) и топологическую сортировку, которые находят широкое применение в сетевых и оптимизационных задачах.
# Алгоритмы хеширования паролей

Безопасное хранение паролей — критически важный аспект веб-разработки. Утечка паролей может привести к катастрофическим последствиям для пользователей и репутации приложения. Именно поэтому понимание и правильное применение алгоритмов хеширования паролей является необходимым навыком для каждого веб-разработчика.

## Почему нельзя хранить пароли в открытом виде?

Хранение паролей в открытом виде (plaintext) создаёт множество рисков:

1. **Внутренние угрозы** — системные администраторы или другие сотрудники могут получить доступ к паролям пользователей
2. **Внешние угрозы** — в случае взлома базы данных злоумышленники получат все пароли мгновенно
3. **Повторное использование паролей** — люди часто используют одинаковые пароли на разных сервисах, поэтому компрометация одного сервиса может привести к компрометации многих аккаунтов

## Что такое хеширование паролей?

**Хеширование** — это процесс преобразования входных данных (пароля) в строку фиксированной длины (хеш), который обладает следующими свойствами:
- **Одностороннее преобразование** — легко вычислить хеш по паролю, но невозможно восстановить пароль по хешу
- **Детерминированность** — одинаковые пароли всегда дают одинаковый хеш (при отсутствии соли)
- **Лавинный эффект** — небольшое изменение входных данных приводит к значительному изменению хеша
- **Устойчивость к коллизиям** — крайне маловероятно, что разные пароли дадут одинаковый хеш

## Основные проблемы простого хеширования

Однако простого применения криптографических хеш-функций (MD5, SHA-1, SHA-256 и т.д.) недостаточно для безопасного хранения паролей. Вот основные проблемы:

1. **Атаки по словарю** — злоумышленник может заранее вычислить хеши для миллионов популярных паролей и быстро сопоставить их с украденной базой данных
2. **Радужные таблицы** — предварительно вычисленные таблицы для обращения хеш-функций
3. **Аппаратные атаки** — современные GPU могут вычислять миллиарды хешей в секунду для обычных хеш-функций

## Свойства безопасных алгоритмов хеширования паролей

Безопасные алгоритмы хеширования паролей должны обладать следующими свойствами:

1. **Использование соли (salt)** — уникальное случайное значение, добавляемое к каждому паролю перед хешированием
2. **Медленное вычисление** — функция должна быть достаточно медленной, чтобы затруднить перебор
3. **Настраиваемая сложность** — возможность регулировать скорость вычисления в зависимости от доступных вычислительных ресурсов
4. **Использование памяти** — требование большого объема памяти для вычисления хеша (препятствует атакам на GPU)

## Современные алгоритмы хеширования паролей

### 1. bcrypt

bcrypt был создан в 1999 году и до сих пор является одним из наиболее распространенных алгоритмов хеширования паролей. Он основан на шифре Blowfish и специально разработан для медленного хеширования паролей.

#### Основные характеристики bcrypt:
- Автоматически генерирует и включает соль в выходную строку
- Имеет параметр стоимости (cost factor), который позволяет увеличивать время вычисления
- Ограничивает длину пароля (обычно до 72 байт)
- Выходной хеш включает в себя все параметры, необходимые для его проверки

#### Реализация в Python с использованием библиотеки `bcrypt`:

```python
import bcrypt

def hash_password(password):
    """
    Хеширует пароль с использованием bcrypt.
    
    Args:
        password (str): Пароль для хеширования
        
    Returns:
        bytes: Хеш пароля с включенной солью и параметрами
    """
    # Преобразуем строку в байты (bcrypt работает с байтами)
    password_bytes = password.encode('utf-8')
    
    # Генерируем соль и хешируем пароль
    # Параметр rounds указывает на сложность (2^rounds итераций)
    # По умолчанию для bcrypt используется 12 (2^12 = 4,096 итераций)
    salt = bcrypt.gensalt(rounds=12)
    hashed = bcrypt.hashpw(password_bytes, salt)
    
    return hashed

def verify_password(password, hashed_password):
    """
    Проверяет соответствие пароля его хешу.
    
    Args:
        password (str): Проверяемый пароль
        hashed_password (bytes): Хеш пароля из базы данных
        
    Returns:
        bool: True, если пароль соответствует хешу, иначе False
    """
    password_bytes = password.encode('utf-8')
    return bcrypt.checkpw(password_bytes, hashed_password)

# Пример использования
password = "mysecretpassword"
hashed_password = hash_password(password)

print(f"Исходный пароль: {password}")
print(f"Хеш пароля: {hashed_password}")

# Проверка правильного пароля
correct = verify_password(password, hashed_password)
print(f"Правильный пароль: {correct}")

# Проверка неправильного пароля
incorrect = verify_password("wrongpassword", hashed_password)
print(f"Неправильный пароль: {incorrect}")
```

### 2. Argon2

Argon2 — победитель конкурса Password Hashing Competition (2015), который был специально разработан для противодействия современным атакам на пароли. Существует в нескольких вариантах (Argon2d, Argon2i и Argon2id), каждый с разными свойствами безопасности.

#### Основные характеристики Argon2:
- Настройка трех параметров: время, память и параллелизм
- Высокая устойчивость к атакам на GPU и ASIC
- Argon2i оптимизирован для защиты от атак по сторонним каналам
- Argon2d обеспечивает максимальную защиту от перебора
- Argon2id — гибридный режим, сочетающий свойства обоих вариантов

#### Реализация в Python с использованием библиотеки `argon2-cffi`:

```python
from argon2 import PasswordHasher
from argon2.exceptions import VerifyMismatchError

def hash_password_argon2(password):
    """
    Хеширует пароль с использованием Argon2.
    
    Args:
        password (str): Пароль для хеширования
        
    Returns:
        str: Хеш пароля с включенной солью и параметрами
    """
    # Создаем экземпляр PasswordHasher с настраиваемыми параметрами
    # time_cost: количество итераций
    # memory_cost: использование памяти (в килобайтах)
    # parallelism: степень параллелизма
    ph = PasswordHasher(
        time_cost=3,       # количество итераций
        memory_cost=65536, # 64 МБ
        parallelism=4,     # степень параллелизма
        hash_len=32,       # длина хеша
        salt_len=16        # длина соли
    )
    
    # Хешируем пароль
    hash_value = ph.hash(password)
    return hash_value

def verify_password_argon2(password, hash_value):
    """
    Проверяет соответствие пароля его хешу Argon2.
    
    Args:
        password (str): Проверяемый пароль
        hash_value (str): Хеш пароля из базы данных
        
    Returns:
        bool: True, если пароль соответствует хешу, иначе False
    """
    ph = PasswordHasher()
    try:
        ph.verify(hash_value, password)
        return True
    except VerifyMismatchError:
        return False

# Пример использования
password = "mysecretpassword"
hashed_password = hash_password_argon2(password)

print(f"Исходный пароль: {password}")
print(f"Хеш пароля (Argon2): {hashed_password}")

# Проверка правильного пароля
correct = verify_password_argon2(password, hashed_password)
print(f"Правильный пароль: {correct}")

# Проверка неправильного пароля
incorrect = verify_password_argon2("wrongpassword", hashed_password)
print(f"Неправильный пароль: {incorrect}")
```

### 3. PBKDF2 (Password-Based Key Derivation Function 2)

PBKDF2 — это алгоритм, основанный на многократном применении хеш-функции (обычно HMAC-SHA256) с использованием соли. Он прост в реализации и имеет хорошую поддержку в большинстве языков и платформ.

#### Основные характеристики PBKDF2:
- Настраиваемое количество итераций для увеличения времени вычисления
- Поддержка разных хеш-функций (SHA-1, SHA-256, SHA-512 и т.д.)
- Соответствие стандартам NIST и PKCS
- Относительно слабая защита от атак на GPU (по сравнению с bcrypt и Argon2)

#### Реализация в Python с использованием стандартной библиотеки:

```python
import os
import binascii
import hashlib

def hash_password_pbkdf2(password, salt=None, iterations=100000):
    """
    Хеширует пароль с использованием PBKDF2.
    
    Args:
        password (str): Пароль для хеширования
        salt (bytes, optional): Соль или None для генерации новой
        iterations (int): Количество итераций
        
    Returns:
        tuple: (хеш пароля в шестнадцатеричном формате, соль в шестнадцатеричном формате, количество итераций)
    """
    # Если соль не передана, генерируем новую
    if salt is None:
        salt = os.urandom(32)  # 32 байта = 256 бит
    elif isinstance(salt, str):
        # Если соль передана в виде строки, преобразуем в байты
        salt = binascii.unhexlify(salt)
    
    # Преобразуем пароль в байты
    password_bytes = password.encode('utf-8')
    
    # Вычисляем хеш с использованием PBKDF2 и SHA-256
    hashed = hashlib.pbkdf2_hmac(
        'sha256',      # Хеш-функция
        password_bytes, # Пароль
        salt,          # Соль
        iterations,    # Количество итераций
        dklen=32       # Длина выходного ключа (32 байта = 256 бит)
    )
    
    # Преобразуем результат и соль в шестнадцатеричный формат для хранения
    hex_hash = binascii.hexlify(hashed).decode('ascii')
    hex_salt = binascii.hexlify(salt).decode('ascii')
    
    return (hex_hash, hex_salt, iterations)

def verify_password_pbkdf2(password, stored_hash, stored_salt, iterations):
    """
    Проверяет соответствие пароля его хешу PBKDF2.
    
    Args:
        password (str): Проверяемый пароль
        stored_hash (str): Хеш пароля из базы данных в шестнадцатеричном формате
        stored_salt (str): Соль из базы данных в шестнадцатеричном формате
        iterations (int): Количество итераций, использованное при хешировании
        
    Returns:
        bool: True, если пароль соответствует хешу, иначе False
    """
    # Вычисляем хеш для проверяемого пароля с теми же параметрами
    verification_hash, _, _ = hash_password_pbkdf2(password, stored_salt, iterations)
    
    # Сравниваем хеши
    return verification_hash == stored_hash

# Функция для хеширования с форматированным выводом в стиле PHC
def create_pbkdf2_hash(password, iterations=100000):
    """
    Создает хеш пароля в формате PHC (Password Hashing Competition).
    
    Args:
        password (str): Пароль для хеширования
        iterations (int): Количество итераций
        
    Returns:
        str: Хеш пароля в формате $pbkdf2-sha256$i=iterations$salt$hash
    """
    hash_value, salt, iterations = hash_password_pbkdf2(password, iterations=iterations)
    return f"$pbkdf2-sha256$i={iterations}${salt}${hash_value}"

def verify_pbkdf2_hash(password, hash_string):
    """
    Проверяет соответствие пароля его хешу в формате PHC.
    
    Args:
        password (str): Проверяемый пароль
        hash_string (str): Хеш пароля в формате PHC
        
    Returns:
        bool: True, если пароль соответствует хешу, иначе False
    """
    # Разбираем строку хеша
    parts = hash_string.split('$')
    if len(parts) != 5 or not parts[1].startswith('pbkdf2-sha256'):
        raise ValueError("Неверный формат хеша")
    
    # Извлекаем параметры
    algorithm = parts[1]
    iterations_part = parts[2]
    salt = parts[3]
    stored_hash = parts[4]
    
    # Проверяем, что строка iterations начинается с i=
    if not iterations_part.startswith('i='):
        raise ValueError("Неверный формат параметра итераций")
    
    # Извлекаем количество итераций
    iterations = int(iterations_part[2:])
    
    # Проверяем пароль
    return verify_password_pbkdf2(password, stored_hash, salt, iterations)

# Пример использования
password = "mysecretpassword"

# Хеширование с использованием PBKDF2 в формате PHC
hash_string = create_pbkdf2_hash(password)
print(f"Исходный пароль: {password}")
print(f"Хеш пароля (PBKDF2): {hash_string}")

# Проверка правильного пароля
correct = verify_pbkdf2_hash(password, hash_string)
print(f"Правильный пароль: {correct}")

# Проверка неправильного пароля
incorrect = verify_pbkdf2_hash("wrongpassword", hash_string)
print(f"Неправильный пароль: {incorrect}")
```

### 4. scrypt

scrypt — алгоритм, разработанный для противодействия атакам с использованием специализированного оборудования (ASIC, GPU). Его основной особенностью является высокое потребление памяти.

#### Основные характеристики scrypt:
- Выраженная зависимость от памяти, делающая аппаратные атаки затруднительными
- Настраиваемые параметры для времени вычисления и использования памяти
- Более высокая защита от атак на GPU по сравнению с PBKDF2
- Сложнее в реализации и имеет меньшую поддержку в стандартных библиотеках

#### Реализация в Python с использованием библиотеки `scrypt`:

```python
import os
import base64
import scrypt

def hash_password_scrypt(password, salt=None, n=16384, r=8, p=1):
    """
    Хеширует пароль с использованием scrypt.
    
    Args:
        password (str): Пароль для хеширования
        salt (bytes, optional): Соль или None для генерации новой
        n (int): Параметр CPU/memory cost
        r (int): Параметр размера блока
        p (int): Параметр параллелизма
        
    Returns:
        tuple: (хеш в base64, соль в base64, параметры n, r, p)
    """
    # Если соль не передана, генерируем новую
    if salt is None:
        salt = os.urandom(32)
    
    # Преобразуем пароль в байты
    password_bytes = password.encode('utf-8')
    
    # Вычисляем хеш с использованием scrypt
    hash_bytes = scrypt.hash(
        password_bytes,
        salt,
        buflen=32,  # Длина выходного ключа (32 байта = 256 бит)
        N=n,        # CPU/memory cost parameter
        r=r,        # Block size parameter
        p=p         # Parallelization parameter
    )
    
    # Преобразуем результат и соль в base64 для хранения
    b64_hash = base64.b64encode(hash_bytes).decode('ascii')
    b64_salt = base64.b64encode(salt).decode('ascii')
    
    return (b64_hash, b64_salt, n, r, p)

def verify_password_scrypt(password, stored_hash, stored_salt, n=16384, r=8, p=1):
    """
    Проверяет соответствие пароля его хешу scrypt.
    
    Args:
        password (str): Проверяемый пароль
        stored_hash (str): Хеш пароля из базы данных в формате base64
        stored_salt (str): Соль из базы данных в формате base64
        n, r, p: Параметры scrypt, использованные при хешировании
        
    Returns:
        bool: True, если пароль соответствует хешу, иначе False
    """
    # Декодируем хеш и соль из base64
    hash_bytes = base64.b64decode(stored_hash)
    salt_bytes = base64.b64decode(stored_salt)
    
    # Преобразуем пароль в байты
    password_bytes = password.encode('utf-8')
    
    # Вычисляем хеш с теми же параметрами
    calculated_hash = scrypt.hash(
        password_bytes,
        salt_bytes,
        buflen=len(hash_bytes),
        N=n,
        r=r,
        p=p
    )
    
    # Сравниваем хеши
    return calculated_hash == hash_bytes

# Функция для хеширования с форматированным выводом в стиле PHC
def create_scrypt_hash(password, n=16384, r=8, p=1):
    """
    Создает хеш пароля в формате PHC.
    
    Args:
        password (str): Пароль для хеширования
        n, r, p: Параметры scrypt
        
    Returns:
        str: Хеш пароля в формате $scrypt$ln=log2(n)$r=r$p=p$salt$hash
    """
    hash_value, salt, n, r, p = hash_password_scrypt(password, n=n, r=r, p=p)
    ln = n.bit_length() - 1  # log2(n)
    return f"$scrypt$ln={ln}$r={r}$p={p}${salt}${hash_value}"

def verify_scrypt_hash(password, hash_string):
    """
    Проверяет соответствие пароля его хешу в формате PHC.
    
    Args:
        password (str): Проверяемый пароль
        hash_string (str): Хеш пароля в формате PHC
        
    Returns:
        bool: True, если пароль соответствует хешу, иначе False
    """
    # Разбираем строку хеша
    parts = hash_string.split('$')
    if len(parts) != 7 or parts[1] != 'scrypt':
        raise ValueError("Неверный формат хеша")
    
    # Извлекаем параметры
    ln_part = parts[2]
    r_part = parts[3]
    p_part = parts[4]
    salt = parts[5]
    stored_hash = parts[6]
    
    # Проверяем и извлекаем параметры
    if not ln_part.startswith('ln='):
        raise ValueError("Неверный формат параметра ln")
    if not r_part.startswith('r='):
        raise ValueError("Неверный формат параметра r")
    if not p_part.startswith('p='):
        raise ValueError("Неверный формат параметра p")
    
    ln = int(ln_part[3:])
    r = int(r_part[2:])
    p = int(p_part[2:])
    n = 1 << ln  # 2^ln
    
    # Проверяем пароль
    return verify_password_scrypt(password, stored_hash, salt, n, r, p)

# Пример использования
password = "mysecretpassword"

# Хеширование с использованием scrypt в формате PHC
hash_string = create_scrypt_hash(password)
print(f"Исходный пароль: {password}")
print(f"Хеш пароля (scrypt): {hash_string}")

# Проверка правильного пароля
correct = verify_scrypt_hash(password, hash_string)
print(f"Правильный пароль: {correct}")

# Проверка неправильного пароля
incorrect = verify_scrypt_hash("wrongpassword", hash_string)
print(f"Неправильный пароль: {incorrect}")
```

## Соль и перец: дополнительные меры защиты

### Соль (Salt)

**Соль** — это случайное значение, которое добавляется к паролю перед хешированием. Соль не является секретной и хранится вместе с хешем.

#### Основные свойства соли:
- Должна быть уникальной для каждого пароля
- Должна быть достаточно длинной (обычно минимум 16 байт)
- Генерируется с использованием криптографически стойкого генератора случайных чисел

#### Зачем нужна соль:
- Предотвращает использование предварительно вычисленных таблиц (rainbow tables)
- Защищает от атак по словарю, так как одинаковые пароли дают разные хеши благодаря разным солям
- Обеспечивает уникальность хешей даже для одинаковых паролей

### Перец (Pepper)

**Перец** — это секретное значение, которое также добавляется к паролю перед хешированием, но в отличие от соли, перец:
- Является одинаковым для всех паролей
- Хранится не в базе данных, а в конфигурации приложения
- Действует как дополнительный уровень защиты

#### Реализация хеширования с использованием соли и перца:

```python
import os
import hmac
import hashlib
import binascii

def hash_with_salt_and_pepper(password, pepper, salt=None, iterations=100000):
    """
    Хеширует пароль с использованием соли и перца.
    
    Args:
        password (str): Пароль для хеширования
        pepper (bytes): Секретный перец
        salt (bytes, optional): Соль или None для генерации новой
        iterations (int): Количество итераций
        
    Returns:
        tuple: (хеш в шестнадцатеричном формате, соль в шестнадцатеричном формате, количество итераций)
    """
    # Если соль не передана, генерируем новую
    if salt is None:
        salt = os.urandom(32)
    
    # Сначала добавляем перец к паролю с использованием HMAC
    password_bytes = password.encode('utf-8')
    peppered_password = hmac.new(pepper, password_bytes, hashlib.sha256).digest()
    
    # Затем хешируем с использованием PBKDF2 и соли
    hashed = hashlib.pbkdf2_hmac(
        'sha256',
        peppered_password,
        salt,
        iterations,
        dklen=32
    )
    
    # Преобразуем результат и соль в шестнадцатеричный формат для хранения
    hex_hash = binascii.hexlify(hashed).decode('ascii')
    hex_salt = binascii.hexlify(salt).decode('ascii')
    
    return (hex_hash, hex_salt, iterations)

def verify_with_salt_and_pepper(password, pepper, stored_hash, stored_salt, iterations):
    """
    Проверяет пароль с учетом соли и перца.
    
    Args:
        password (str): Проверяемый пароль
        pepper (bytes): Секретный перец
        stored_hash (str): Хеш пароля из базы данных в шестнадцатеричном формате
        stored_salt (str): Соль из базы данных в шестнадцатеричном формате
        iterations (int): Количество итераций, использованное при хешировании
        
    Returns:
        bool: True, если пароль соответствует хешу, иначе False
    """
    # Преобразуем соль из шестнадцатеричного формата
    salt = binascii.unhexlify(stored_salt)
    
    # Вычисляем хеш для проверяемого пароля с теми же параметрами
    verification_hash, _, _ = hash_with_salt_and_pepper(
        password, pepper, salt, iterations
    )
    
    # Сравниваем хеши
    return verification_hash == stored_hash

# Пример использования
password = "mysecretpassword"
pepper = os.urandom(32)  # В реальном приложении перец хранится в конфигурации

# Хеширование с солью и перцем
hash_value, salt, iterations = hash_with_salt_and_pepper(password, pepper)
print(f"Исходный пароль: {password}")
print(f"Хеш пароля: {hash_value}")
print(f"Соль: {salt}")
print(f"Итерации: {iterations}")

# Проверка правильного пароля
correct = verify_with_salt_and_pepper(password, pepper, hash_value, salt, iterations)
print(f"Правильный пароль: {correct}")

# Проверка неправильного пароля
incorrect = verify_with_salt_and_pepper("wrongpassword", pepper, hash_value, salt, iterations)
print(f"Неправильный пароль: {incorrect}")
```

## Практическое применение в веб-разработке

### 1. Регистрация пользователя в веб-приложении

```python
import bcrypt
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///users.db'
db = SQLAlchemy(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.LargeBinary(60), nullable=False)
    
    def __repr__(self):
        return f'<User {self.username}>'

def setup_database():
    """Создает таблицы в базе данных."""
    with app.app_context():
        db.create_all()

@app.route('/register', methods=['POST'])
def register():
    """Регистрирует нового пользователя."""
    data = request.get_json()
    
    # Проверяем наличие необходимых полей
    if not all(k in data for k in ('username', 'email', 'password')):
        return jsonify({'error': 'Missing required fields'}), 400
    
    # Проверяем, что пользователь с таким именем или email не существует
    if User.query.filter_by(username=data['username']).first():
        return jsonify({'error': 'Username already taken'}), 409
    
    if User.query.filter_by(email=data['email']).first():
        return jsonify({'error': 'Email already registered'}), 409
    
    # Хешируем пароль с использованием bcrypt
    password_hash = bcrypt.hashpw(
        data['password'].encode('utf-8'),
        bcrypt.gensalt(rounds=12)
    )
    
    # Создаем нового пользователя
    new_user = User(
        username=data['username'],
        email=data['email'],
        password_hash=password_hash
    )
    
    # Сохраняем пользователя в базе данных
    db.session.add(new_user)
    db.session.commit()
    
    return jsonify({'message': 'User registered successfully'}), 201

@app.route('/login', methods=['POST'])
def login():
    """Авторизует пользователя."""
    data = request.get_json()
    
    # Проверяем наличие необходимых полей
    if not all(k in data for k in ('username', 'password')):
        return jsonify({'error': 'Missing required fields'}), 400
    
    # Ищем пользователя в базе данных
    user = User.query.filter_by(username=data['username']).first()
    
    # Если пользователь не найден или пароль неверен
    if not user or not bcrypt.checkpw(
        data['password'].encode('utf-8'),
        user.password_hash
    ):
        return jsonify({'error': 'Invalid username or password'}), 401
    
    # В реальном приложении здесь бы создавался и возвращался токен авторизации
    return jsonify({'message': 'Login successful', 'user_id': user.id}), 200

# Запускаем приложение
if __name__ == '__main__':
    setup_database()
    app.run(debug=True)
```

### 2. Смена пароля и проверка его сложности

```python
import re
import bcrypt
from flask import Flask, request, jsonify, g
from flask_sqlalchemy import SQLAlchemy
from flask_httpauth import HTTPTokenAuth

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///users.db'
db = SQLAlchemy(app)
auth = HTTPTokenAuth(scheme='Bearer')

# Имитация хранилища токенов (в реальном приложении это должна быть база данных или Redis)
tokens = {}

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.LargeBinary(60), nullable=False)

@auth.verify_token
def verify_token(token):
    """Проверяет токен авторизации."""
    if token in tokens:
        g.user_id = tokens[token]
        return True
    return False

def password_strength_check(password):
    """
    Проверяет сложность пароля.
    
    Args:
        password (str): Проверяемый пароль
        
    Returns:
        tuple: (bool, str) - Результат проверки и сообщение
    """
    # Минимальная длина
    if len(password) < 8:
        return False, "Password must be at least 8 characters long"
    
    # Наличие букв в разных регистрах
    if not re.search(r'[a-z]', password) or not re.search(r'[A-Z]', password):
        return False, "Password must contain both lowercase and uppercase letters"
    
    # Наличие цифр
    if not re.search(r'\d', password):
        return False, "Password must contain at least one digit"
    
    # Наличие специальных символов
    if not re.search(r'[\W_]', password):
        return False, "Password must contain at least one special character"
    
    return True, "Password meets all requirements"

@app.route('/change-password', methods=['POST'])
@auth.login_required
def change_password():
    """Изменяет пароль пользователя."""
    data = request.get_json()
    
    # Проверяем наличие необходимых полей
    if not all(k in data for k in ('current_password', 'new_password')):
        return jsonify({'error': 'Missing required fields'}), 400
    
    # Получаем пользователя
    user = User.query.get(g.user_id)
    if not user:
        return jsonify({'error': 'User not found'}), 404
    
    # Проверяем текущий пароль
    if not bcrypt.checkpw(
        data['current_password'].encode('utf-8'),
        user.password_hash
    ):
        return jsonify({'error': 'Current password is incorrect'}), 401
    
    # Проверяем сложность нового пароля
    valid, message = password_strength_check(data['new_password'])
    if not valid:
        return jsonify({'error': message}), 400
    
    # Хешируем новый пароль
    new_password_hash = bcrypt.hashpw(
        data['new_password'].encode('utf-8'),
        bcrypt.gensalt(rounds=12)
    )
    
    # Обновляем пароль пользователя
    user.password_hash = new_password_hash
    db.session.commit()
    
    return jsonify({'message': 'Password changed successfully'}), 200

# Дополнительный маршрут для проверки сложности пароля
@app.route('/check-password-strength', methods=['POST'])
def check_password_strength():
    """Проверяет сложность пароля без сохранения."""
    data = request.get_json()
    
    if 'password' not in data:
        return jsonify({'error': 'Missing password field'}), 400
    
    valid, message = password_strength_check(data['password'])
    
    return jsonify({
        'valid': valid,
        'message': message
    })

# Запускаем приложение
if __name__ == '__main__':
    app.run(debug=True)
```

### 3. Защита от распространенных атак

#### Защита от атак перебором (брутфорс) с использованием временных ограничений

```python
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy
import bcrypt
import time
from datetime import datetime, timedelta

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///users.db'
db = SQLAlchemy(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    password_hash = db.Column(db.LargeBinary(60), nullable=False)
    failed_login_attempts = db.Column(db.Integer, default=0)
    last_failed_login = db.Column(db.DateTime, nullable=True)
    account_locked_until = db.Column(db.DateTime, nullable=True)

@app.route('/login', methods=['POST'])
def login():
    """Авторизует пользователя с защитой от перебора."""
    data = request.get_json()
    
    # Проверяем наличие необходимых полей
    if not all(k in data for k in ('username', 'password')):
        return jsonify({'error': 'Missing required fields'}), 400
    
    # Ищем пользователя в базе данных
    user = User.query.filter_by(username=data['username']).first()
    
    # Если пользователь не найден, возвращаем ошибку
    # (В реальном приложении лучше не уточнять причину ошибки авторизации)
    if not user:
        return jsonify({'error': 'Invalid username or password'}), 401
    
    # Проверяем, не заблокирована ли учетная запись
    now = datetime.utcnow()
    if user.account_locked_until and user.account_locked_until > now:
        lock_time_remaining = (user.account_locked_until - now).total_seconds()
        return jsonify({
            'error': 'Account is temporarily locked',
            'lockTimeRemaining': int(lock_time_remaining)
        }), 429
    
    # Проверяем пароль
    password_correct = bcrypt.checkpw(
        data['password'].encode('utf-8'),
        user.password_hash
    )
    
    if not password_correct:
        # Увеличиваем счетчик неудачных попыток
        user.failed_login_attempts += 1
        user.last_failed_login = now
        
        # Определяем, нужно ли временно заблокировать аккаунт
        if user.failed_login_attempts >= 5:
            # Экспоненциальная задержка в зависимости от количества неудачных попыток
            delay_minutes = min(2 ** (user.failed_login_attempts - 5), 1440)  # не более 24 часов
            user.account_locked_until = now + timedelta(minutes=delay_minutes)
            
            db.session.commit()
            
            return jsonify({
                'error': 'Too many failed login attempts. Account locked temporarily.',
                'lockTimeMinutes': delay_minutes
            }), 429
        
        db.session.commit()
        
        # Вводим искусственную задержку для предотвращения атак по времени
        time.sleep(0.5)
        
        return jsonify({'error': 'Invalid username or password'}), 401
    
    # Если авторизация успешна, сбрасываем счетчик неудачных попыток
    user.failed_login_attempts = 0
    user.last_failed_login = None
    user.account_locked_until = None
    db.session.commit()
    
    # В реальном приложении здесь бы создавался и возвращался токен авторизации
    return jsonify({'message': 'Login successful', 'user_id': user.id}), 200

# Запускаем приложение
if __name__ == '__main__':
    app.run(debug=True)
```

## Сравнение алгоритмов хеширования паролей

Для выбора подходящего алгоритма хеширования паролей важно понимать их сильные и слабые стороны:

| Алгоритм | Преимущества | Недостатки | Рекомендации |
|----------|--------------|------------|--------------|
| **bcrypt** | - Проверенная временем надежность<br>- Автоматическая соль<br>- Настраиваемая сложность<br>- Широкая поддержка | - Ограничение длины пароля<br>- Ограниченное использование памяти | Хороший выбор для большинства приложений |
| **Argon2** | - Победитель конкурса PHC<br>- Настройка времени, памяти и параллелизма<br>- Лучшая защита от атак на GPU и ASIC | - Меньшая распространенность<br>- Сложнее настраивать | Лучший выбор для новых проектов с высокими требованиями к безопасности |
| **PBKDF2** | - Стандартизован (NIST, PKCS)<br>- Простая реализация<br>- Широкая поддержка<br>- Нет ограничений на длину пароля | - Слабая защита от атак на GPU<br>- Требуется больше итераций для сопоставимой безопасности | Подходит для случаев, когда важна совместимость со стандартами |
| **scrypt** | - Высокое использование памяти<br>- Хорошая защита от атак на специализированном оборудовании | - Сложная настройка параметров<br>- Меньшая распространенность | Хороший выбор, когда требуется усиленная защита от аппаратных атак |

### Рекомендации по параметрам алгоритмов

#### bcrypt
- Начальное значение cost factor: 12 (2^12 = 4,096 итераций)
- Увеличивайте cost factor на 1 каждые 1-2 года
- Целевое время вычисления хеша: около 250 мс

#### Argon2
- Начальные параметры: time_cost=3, memory_cost=65536 (64 МБ), parallelism=4
- Целевое время вычисления хеша: около 500 мс
- Настраивайте параметры с учетом доступной памяти на сервере

#### PBKDF2
- Начальное количество итераций: минимум 100,000
- Удваивайте количество итераций каждые 1-2 года
- Целевое время вычисления хеша: около 500 мс

#### scrypt
- Начальные параметры: N=16384, r=8, p=1
- Целевое время вычисления хеша: около 500 мс
- Настраивайте параметры с учетом доступной памяти на сервере

## Лучшие практики хранения паролей

1. **Никогда не храните пароли в открытом виде** — всегда используйте хеширование с солью
2. **Используйте современные алгоритмы хеширования** — bcrypt, Argon2 или scrypt для новых проектов
3. **Применяйте уникальную соль для каждого пароля** — генерируйте её с помощью криптографически стойкого генератора случайных чисел
4. **Добавьте перец для дополнительной защиты** — храните его отдельно от базы данных
5. **Регулярно обновляйте параметры хеширования** — увеличивайте сложность с ростом вычислительных мощностей
6. **Введите политику сложности паролей** — требуйте использования сложных паролей
7. **Реализуйте защиту от перебора** — ограничивайте количество попыток входа
8. **Обрабатывайте вход без утечки информации** — не указывайте, что именно неверно: логин или пароль
9. **Обеспечьте безопасную смену пароля** — требуйте ввода текущего пароля
10. **Используйте дополнительные факторы аутентификации** — двухфакторная аутентификация (2FA) значительно повышает безопасность

## Заключение

Правильное хеширование паролей — это фундаментальный аспект безопасности веб-приложений. Использование современных алгоритмов хеширования, таких как bcrypt, Argon2 или scrypt, в сочетании с другими мерами безопасности позволяет значительно снизить риск компрометации учетных данных пользователей даже в случае утечки базы данных.

Помните, что безопасность — это непрерывный процесс, а не одноразовое мероприятие. Регулярно обновляйте параметры хеширования, следите за новыми уязвимостями и рекомендациями по безопасности, и всегда отдавайте предпочтение проверенным и стандартизированным решениям.

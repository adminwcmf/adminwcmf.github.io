---
layout: post
title: "我珍藏5年的10个Python高效技巧，90%的人不知道 🔥"
date: 2026-02-04 20:00:00 +0800
categories: 
  - 代码技巧
tags:
  - Python
  - 编程技巧
  - 高效编程
  - 代码优化
  - 程序员
excerpt: "5年Python开发经验总结，10个让你代码效率提升10倍的高级技巧。学会这些，你就是团队里最靓的仔。建议收藏后反复阅读。"
header:
  overlay_image: https://images.unsplash.com/photo-1526374965328-7f61d4dc18c5?w=1920
  overlay_filter: 0.6
  teaser: https://images.unsplash.com/photo-1526374965328-7f61d4dc18c5?w=500
toc: true
toc_sticky: true
---

# 我珍藏5年的10个Python高效技巧，90%的人不知道 🔥

## 前言 💡

写Python已经5年了，从最初的"能运行就行"到现在的"优雅且高效"，我踩过无数的坑，也发现了许多不为人知的高级技巧。

今天分享10个我觉得最实用、但大多数人不知道的Python技巧。

**注意：** 这些不是入门知识，而是进阶技巧。建议收藏后反复阅读。

## 1. Walrus Operator（海象运算符）🦅

Python 3.8引入的海象运算符，让我写代码时少写了很多变量。

**传统写法：**
```python
# 每次循环都要计算length
data = [1, 2, 3, 4, 5]
result = []
for item in data:
    length = len(str(item))  # 重复计算
    if length > 1:
        result.append(item)
```

**使用海象运算符：**
```python
data = [1, 2, 3, 4, 5]
result = []
for item in data:
    if (length := len(str(item))) > 1:
        result.append(item)
```

**更实用的场景：**
```python
# 读取文件时检查内容
with open('data.txt') as f:
    while (line := f.readline()):
        process(line)

# 正则匹配
import re
pattern = re.compile(r'\d+')
text = "Today is 2026-02-04"
while (match := pattern.search(text)):
    print(f"Found: {match.group()}")
    text = text[match.end():]
```

**关键点：** 只能在表达式内部使用，不能单独作为语句。

## 2. Generator Expressions（生成器表达式）💨

如果你处理大数据集还在用列表推导式，那你需要了解一下生成器。

**区别：**
```python
# 列表推导式（一次性生成所有数据）
squares_list = [x**2 for x in range(1000000)]
# 内存占用：约8MB

# 生成器表达式（惰性计算）
squares_gen = (x**2 for x in range(1000000))
# 内存占用：约80字节
```

**实际应用场景：**
```python
# 场景1：读取大文件
def read_large_file(file_path):
    # 不会一次性加载整个文件到内存
    return (line for line in open(file_path, 'r'))

# 场景2：管道处理数据
data = (
    line.strip() 
    for line in open('data.txt') 
    if line.strip()
)
processed = (
    int(x) 
    for x in data 
    if x.isdigit()
)
result = sum(processed)

# 场景3：无限序列
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

# 只取前10个，不需要生成整个序列
fib = fibonacci()
first_10 = [next(fib) for _ in range(10)]
```

**记忆方法：** 用小括号`()`代替中括号`[]`，就是生成器。

## 3. Defaultdict for Safe Access🔐

处理嵌套字典时，`defaultdict`可以让你少写很多`if key in dict`的判断。

**传统写法：**
```python
data = {}
for item in items:
    category = item['category']
    if category not in data:
        data[category] = []
    data[category].append(item)
```

**使用defaultdict：**
```python
from collections import defaultdict

data = defaultdict(list)
for item in items:
    data[item['category']].append(item)

# 转换为普通字典
data = dict(data)
```

**更复杂的例子：**
```python
from collections import defaultdict, Counter

# 默认值为int（计数器）
word_count = defaultdict(int)
for word in ['apple', 'banana', 'apple', 'cherry', 'banana', 'apple']:
    word_count[word] += 1

# 默认值为dict（嵌套字典）
nested = defaultdict(lambda: defaultdict(int))
nested['user1']['score'] = 100
nested['user2']['score'] = 95

# 默认值为set（去重）
unique_items = defaultdict(set)
for item in [1, 2, 1, 3, 2, 1, 4]:
    unique_items['numbers'].add(item)
```

## 4. Counter for Frequency Analysis📊

`Counter`是统计元素频率的神器，比自己写循环快多了。

**基本用法：**
```python
from collections import Counter

# 统计列表元素频率
colors = ['red', 'blue', 'red', 'green', 'blue', 'blue']
counter = Counter(colors)
# Counter({'blue': 3, 'red': 2, 'green': 1})

# 统计单词频率
text = "apple banana apple cherry banana apple"
words = text.split()
word_freq = Counter(words)
# Counter({'apple': 3, 'banana': 2, 'cherry': 1})
```

**高级用法：**
```python
# 最常见的n个元素
most_common = counter.most_common(3)

# 计数器运算
c1 = Counter(['a', 'b', 'c', 'a'])
c2 = Counter(['a', 'b', 'b', 'c', 'c', 'c'])

# 合并
c1 + c2  # Counter({'c': 4, 'a': 2, 'b': 2})
# 减去
c2 - c1  # Counter({'b': 1, 'c': 2})
# 交集
c1 & c2  # Counter({'a': 1, 'b': 1})

# 实际应用：分析日志
log_lines = open('app.log').readlines()
status_codes = Counter(line.split()[-1] for line in log_lines)
print(status_codes.most_common(5))
```

## 5. Context Managers（上下文管理器）🎯

`with`语句不仅适用于文件操作，还可以用于任何需要"清理"操作的场景。

**自定义上下文管理器：**
```python
class Timer:
    def __enter__(self):
        self.start = time.time()
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        self.end = time.time()
        self.duration = self.end - self.start
        print(f"执行时间: {self.duration:.4f}秒")

# 使用
with Timer():
    time.sleep(1)  # 执行耗时操作

# 更简洁的方式：使用contextlib
from contextlib import contextmanager
import time

@contextmanager
def timer():
    start = time.time()
    try:
        yield
    finally:
        end = time.time()
        print(f"执行时间: {end - start:.4f}秒")

with timer():
    sum(range(1000000))
```

**实际应用场景：**
```python
# 场景1：临时修改设置
from contextlib import contextmanager

@contextmanager
def decimal_precision(precision):
    original = getcontext().prec
    getcontext().prec = precision
    try:
        yield
    finally:
        getcontext().prec = original

# 使用
with decimal_precision(4):
    result = Decimal('1.23456789') / Decimal('3')

# 场景2：锁管理
from threading import Lock

lock = Lock()

@contextmanager
def lock_manager(lock):
    lock.acquire()
    try:
        yield
    finally:
        lock.release()

# 场景3：数据库事务
@contextmanager
def transaction(db):
    db.begin()
    try:
        yield db
        db.commit()
    except Exception:
        db.rollback()
        raise
```

## 6. Decorators Factory（装饰器工厂）🏭

装饰器可以带参数，这让代码复用更加强大。

**基础装饰器：**
```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Before")
        result = func(*args, **kwargs)
        print("After")
        return result
    return wrapper

@my_decorator
def greet(name):
    print(f"Hello, {name}!")
```

**带参数的装饰器工厂：**
```python
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def say_hello():
    print("Hello!")

# 执行say_hello()会打印3次"Hello!"
```

**实际应用场景：**
```python
# 场景1：缓存装饰器
from functools import lru_cache
import time

def cache_with_ttl(ttl_seconds=300):
    def decorator(func):
        cache = {}
        
        def wrapper(*args):
            now = time.time()
            if args in cache:
                result, timestamp = cache[args]
                if now - timestamp < ttl_seconds:
                    return result
            
            result = func(*args)
            cache[args] = (result, now)
            return result
        
        return wrapper
    return decorator

@cache_with_ttl(ttl_seconds=60)
def expensive_computation(x):
    time.sleep(1)  # 模拟耗时操作
    return x ** 2

# 场景2：权限检查
def require_role(role):
    def decorator(func):
        def wrapper(user, *args, **kwargs):
            if user.role != role:
                raise PermissionError(f"需要 {role} 权限")
            return func(user, *args, **kwargs)
        return wrapper
    return decorator

@require_role('admin')
def delete_user(user, user_id):
    pass

# 场景3：日志装饰器
def log_calls(level='INFO'):
    def decorator(func):
        def wrapper(*args, **kwargs):
            logger.log(level, f"Calling {func.__name__}")
            return func(*args, **kwargs)
        return wrapper
    return decorator

@log_calls(level='DEBUG')
def process_data(data):
    pass
```

## 7. Named Tuples（命名元组）📛

比普通元组更易读，比类更轻量。

**基础用法：**
```python
from collections import namedtuple

# 定义命名元组
Point = namedtuple('Point', ['x', 'y'])
p = Point(10, 20)

# 访问方式
print(p.x)      # 10
print(p.y)      # 20
print(p[0])     # 10（仍然支持索引）

# 解包
x, y = p
```

**进阶用法：**
```python
# 指定字段别名
Person = namedtuple('Person', ['name', 'age'], defaults=[0])
p = Person('Alice', 30)

# 使用_make创建新实例
p2 = p._make(['Bob', 25])

# 使用_asdict转换为字典
p_dict = p._asdict()

# 使用_replace创建修改后的副本
p3 = p._replace(age=31)

# 继承命名元组
class Point3D(Point):
    __slots__ = ()  # 防止动态添加属性
    
    def distance(self, other):
        return ((self.x - other.x)**2 + (self.y - other.y)**2)**0.5

# 实际应用：数据类
from typing import NamedTuple

class User(NamedTuple):
    id: int
    name: str
    email: str
    is_active: bool = True

users = [
    User(1, 'Alice', 'alice@example.com'),
    User(2, 'Bob', 'bob@example.com'),
]
```

## 8. Zip for Parallel Iteration🔄

同时遍历多个列表，`zip`是最优雅的方式。

**基础用法：**
```python
names = ['Alice', 'Bob', 'Charlie']
ages = [25, 30, 35]
cities = ['Beijing', 'Shanghai', 'Guangzhou']

# 打包成元组
for person in zip(names, ages, cities):
    print(person)
# ('Alice', 25, 'Beijing')
# ('Bob', 30, 'Shanghai')
# ('Charlie', 35, 'Guangzhou')
```

**高级用法：**
```python
# 解压
pairs = [('a', 1), ('b', 2), ('c', 3)]
letters, numbers = zip(*pairs)

# 构建字典
dict(zip(names, ages))

# 处理不同长度的列表
# zip会在最短列表结束时停止
list(zip([1, 2, 3], ['a', 'b']))  # [(1, 'a'), (2, 'b')]

# 使用itertools.zip_longest处理不同长度
from itertools import zip_longest
list(zip_longest([1, 2, 3], ['a', 'b', 'c', 'd'], fillvalue=None))
# [(1, 'a'), (2, 'b'), (3, 'c'), (None, 'd')]

# 实际应用：数据对齐
headers = ['name', 'age', 'city']
values = ['Alice', '25', 'Beijing']
for header, value in zip(headers, values):
    print(f"{header}: {value}")

# 同时更新多个列表
prices = [100, 200, 300]
discounts = [0.1, 0.2, 0.15]
for i in range(len(prices)):
    prices[i] *= (1 - discounts[i])
```

## 9. Type Hints（类型提示）📝

Python 3.5+支持类型提示，让代码自文档化，IDE支持更好。

**基础类型提示：**
```python
def greet(name: str) -> str:
    return f"Hello, {name}!"

def add(a: int, b: int) -> int:
    return a + b
```

**复杂类型提示：**
```python
from typing import List, Dict, Tuple, Optional, Union, Callable

# 列表
def process_items(items: List[int]) -> List[int]:
    return [x * 2 for x in items]

# 字典
def get_user_info(user_id: int) -> Dict[str, Union[str, int]]:
    return {'name': 'Alice', 'age': 25}

# 可选类型
def find_user(name: Optional[str] = None) -> Optional[int]:
    if name:
        return 1
    return None

# 回调函数
def apply_func(func: Callable[[int], int], value: int) -> int:
    return func(value)

# 泛型
from typing import TypeVar, Generic
T = TypeVar('T')

class Stack(Generic[T]):
    def __init__(self) -> None:
        self.items: List[T] = []
    
    def push(self, item: T) -> None:
        self.items.append(item)
    
    def pop(self) -> Optional[T]:
        return self.items.pop() if self.items else None
```

**实际应用：**
```python
# 使用pydantic进行运行时类型检查
from pydantic import BaseModel, ValidationError

class User(BaseModel):
    name: str
    age: int
    email: Optional[str] = None

# 自动类型验证
user = User(name="Alice", age="25")  # age会被转换为int
print(user)  # name='Alice' age=25 email=None

# 使用mypy进行静态类型检查
# mypy script.py
```

## 10. itertools（迭代器工具箱）🧰

Python标准库中最强大的模块之一。

**常用函数：**
```python
import itertools

# chain：连接多个迭代器
list(itertools.chain([1, 2, 3], ['a', 'b', 'c']))
# [1, 2, 3, 'a', 'b', 'c']

# cycle：无限循环
counter = 0
for item in itertools.cycle(['A', 'B']):
    print(item)
    counter += 1
    if counter > 4:  # A B A B A
        break

# islice：切片迭代器
list(itertools.islice(range(10), 2, 8, 2))  # [2, 4, 6]

# combinations：组合
list(itertools.combinations([1, 2, 3, 4], 2))
# [(1, 2), (1, 3), (1, 4), (2, 3), (2, 4), (3, 4)]

# permutations：排列
list(itertools.permutations([1, 2, 3], 2))
# [(1, 2), (1, 3), (2, 1), (2, 3), (3, 1), (3, 2)]

# product：笛卡尔积
list(itertools.product('AB', 'xy'))
# [('A', 'x'), ('A', 'y'), ('B', 'x'), ('B', 'y')]

# groupby：分组
data = [('a', 1), ('a', 2), ('b', 3), ('b', 4)]
for key, group in itertools.groupby(data, lambda x: x[0]):
    print(f"{key}: {list(group)}")
# a: [('a', 1), ('a', 2)]
# b: [('b', 3), ('b', 4)]
```

**实际应用场景：**
```python
# 生成密码字典
chars = 'abc'
numbers = '123'
all_combos = [''.join(p) for p in itertools.product(chars, numbers)]
# a1, a2, a3, b1, b2, b3, c1, c2, c3

# 分页处理
def paginate(items, page_size):
    it = iter(items)
    while True:
        page = list(itertools.islice(it, page_size))
        if not page:
            break
        yield page

# 数据去重（保持顺序）
def unique(iterable):
    seen = set()
    for item in iterable:
        if item not in seen:
            seen.add(item)
            yield item

# 滑动窗口
def sliding_window(iterable, n):
    iters = itertools.tee(iterable, n)
    for i, it in enumerate(iters):
        next(itertools.islice(it, i, None), None)
    return zip(*iters)

list(sliding_window([1, 2, 3, 4, 5], 3))
# [(1, 2, 3), (2, 3, 4), (3, 4, 5)]
```

## Bonus：代码组织建议 📦

### 项目结构
```
my_project/
├── src/
│   ├── __init__.py
│   ├── module1.py
│   └── module2.py
├── tests/
│   ├── __init__.py
│   └── test_module1.py
├── docs/
├── requirements.txt
├── setup.py
└── README.md
```

### 虚拟环境
```bash
python -m venv venv
source venv/bin/activate  # Linux/Mac
# 或
.\venv\Scripts\activate   # Windows

pip install -r requirements.txt
```

### 依赖管理
```bash
# 生成requirements.txt
pip freeze > requirements.txt

# 安装依赖（从requirements.txt）
pip install -r requirements.txt
```

## 结语 🎉

掌握这些技巧，你的Python代码会变得更加：
- **简洁**：用更少的代码表达同样的意思
- **高效**：更好的性能和资源利用
- **可读**：清晰的代码结构
- **专业**：展示你的Python功底

**建议学习路径：**
1. 先在日常代码中使用这些技巧
2. 阅读优秀开源项目的源码
3. 深入理解每个技巧的原理
4. 创造自己的技巧和模式

**记住：** 技巧是为了让代码更好，而不是为了炫技。如果简单的方式能解决问题，就用简单的方式。

---

*你有什么珍藏的Python技巧吗？欢迎在评论区分享！* 🐕

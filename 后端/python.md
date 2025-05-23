# python经验总结
### Enum 枚举类的使用

当你想要定制 Enum 成员的实际值时必须使用 __new__()。 任何其他修改可以用 __new__() 也可以用 __init__()，应优先使用 __init__()。

举例来说，如果你要向构造器传入多个条目，但只希望将其中一个作为值:

``` python
class Coordinate(bytes, Enum):
    """
    Coordinate with binary codes that can be indexed by the int code.
    """
    def __new__(cls, value, label, unit):
        obj = bytes.__new__(cls, [value])
        obj._value_ = value
        obj.label = label
        obj.unit = unit
        return obj
    PX = (0, 'P.X', 'km')
    PY = (1, 'P.Y', 'km')
    VX = (2, 'V.X', 'km/s')
    VY = (3, 'V.Y', 'km/s')


print(Coordinate['PY'])
Coordinate.PY

print(Coordinate(3))
Coordinate.VY

obj = bytes.__new__(cls, value)    # 带有value时，obj=value; 不传value时,obj=""
```
```
> 警告 不要 调用 super().__new__()，因为只能找到仅用于查找的 __new__；请改为直接使用该数据类型。
```
支持的 \_\_dunder__ 名称

\_\_members__ 是一个 member\_name:member 条目的只读有序映射。 它只在类上可用。

如果指定了 \_\_new__()，它必须创建并返回枚举成员；相应地设定成员的 \_value_ 也是一个很好的主意。 一旦所有成员都创建完成它就不会再被使用。

支持的 \_sunder_ 名称
 - \_name_ -- 成员的名称

 - \_value_ -- 成员的值；可以在 \_\_new__ 中设置 / 修改

 - \_missing_ -- 当未发现某个值时所使用的查找函数；可被重写

 - \_ignore_ -- 一个名称列表，可以为 list 或 str，它不会被转化为成员，并将从最终类中被移除

 - \_order_ -- 用于 Python 2/3 代码以确保成员顺序一致（类属性，在类创建期间会被移除）

 - \_generate\_next\_value\_ -- Functional API 和 auto 用它为枚举成员获取适当的值；可被重写

### dict 的使用
连续使用get()方法，要确保中间的get方法都能取到值，保证后续的get方法操作对象为dict，**否则可能出现(AttributeError: 'NoneType' object has no attribute 'get')**
``` python
dict.get("key1", {}).get("key2", {}).get("key3")
```


### try-catch
抛出异常时，可以用raise Exception()提示具体信息
```
raise Exception("提示信息")
```

### fdopen 和普通 open
1.理解 os.fdopen 的作用：

os.fdopen 是Python中用于打开文件的一个函数，但它底层直接调用了操作系统提供的文件描述符（FD）接口。这使得操作更加低层次，提供了比高阶的open()函数更多的控制和更低的层面上的操作。

2.比较 fdopen 和普通 open 的区别：

通常在Python中使用with open(filename, mode) as f:来打开文件，并使用File Object进行操作。
使用低级方法（如fdopen）的好处在于：可能更高效地控制底层的操作，尤其是对于大量数据或高性能场景中的I/O操作。因为File Object本身可能涉及一些额外的管理开销。

3.评估性能优化的潜在：

在某些高性能的应用中，低层操作可以更快地完成文件读写操作。fdopen可以直接控制操作系统对文件的操作流程，避免高一层的中间态（如File Object）带来的延迟或开销。

4.考虑并发处理的可能性：

当需要同时打开多个文件或频繁操作文件时，使用低级操作符可以提高系统的吞吐量和响应速度。这在多任务环境或高并发情况下特别有用。

5.避免资源竞争的问题：

在某些操作系统中，File Object可能有与文件描述符的冲突或者导致资源竞争问题。直接使用低级方法可以在一定程度上规避这些问题，确保操作的一致性和稳定性。

**综上所述，os.fdopen 的使用在这种情况下的主要优点是提升操作效率、避免潜在的资源竞储以及在高负载或复杂IO操作中保持系统的响应能力。

### configparser --- 配置文件解析器
此模块提供了它实现一种基本配置语言 ConfigParser 类，这种语言所提供的结构与 Microsoft Windows INI 文件的类似。 你可以使用这种语言来编写能够由最终用户来自定义的 Python 程序。
```
config = configparser.ConfigParser()
config.sections()
---[]
config.read('example.ini')
---['example.ini']
config.sections()
---['forge.example', 'topsecret.server.example']
```

### 加载环境变量
```
from dotenv import load_dotenv
env_file = '.env.dev'
# 运行环境不为空时按命令行参数加载对应.env文件
if run_env != '':
    env_file = f'.env.{run_env}'
# 加载配置
load_dotenv(env_file)
```
**功能**：这段代码的主要功能是从指定的文件中加载环境变量。环境变量通常用于存储配置信息，如数据库的URL、密码、API密钥等，这些信息在代码中需要使用，但我们不希望这些信息直接写在代码中，以防被泄露。

**目的**：这段代码的目的是为了提高代码的安全性和可维护性。通过加载环境变量，我们可以将这些敏感信息与代码分离，避免在代码中硬编码这些信息。

**使用场景**：这段代码通常用于开发过程中，特别是在需要处理敏感信息的情况下。例如，在开发一个需要连接数据库的应用时，我们可以将数据库的URL、用户名和密码等信息存储在环境变量中，然后在代码中通过加载环境变量的方式来获取这些信息。

**主要逻辑**：这段代码的主要逻辑是调用load_dotenv函数，将指定文件中的环境变量加载到当前的环境中。load_dotenv函数通常是python-dotenv库中的一个函数，它会读取指定文件中的内容，并将其中的环境变量加载到当前的环境中。这样，我们就可以在代码中通过os.getenv函数来获取这些环境变量的值。

### Least Recently Used (最近最少使用) 缓存
    @lru_cache()是Python的一个装饰器，它的全称是Least Recently Used (最近最少使用) 缓存。这是一种缓存策略，用于缓存函数的计算结果，以便在后续调用中重复使用。

**功能**：@lru_cache()装饰器的主要功能是提高函数的执行效率。当一个函数被多次调用，且参数相同时，@lru_cache()装饰器会将函数的结果缓存起来，当下次再次调用该函数且参数相同时，会直接从缓存中获取结果，而不是重新执行函数。

**目的**：@lru_cache()装饰器的目的是为了提高函数的执行效率，尤其是在函数执行时间较长，且被频繁调用的情况下。

**使用场景**：@lru_cache()装饰器常用于以下场景：

- 当一个函数的计算结果在后续调用中会被多次使用，且计算结果不会随着时间改变时。
- 当一个函数的计算时间较长，且被频繁调用时。
  
**主要逻辑**：

- 当一个函数被@lru_cache()装饰后，每次调用该函数时，首先会检查该函数的参数是否已经在缓存中。
- 如果参数已经在缓存中，则直接从缓存中获取结果并返回。
- 如果参数不在缓存中，则执行函数，并将函数的结果和参数一起存入缓存中。
- 当缓存的大小超过设定的最大值时，会丢弃最近最少使用的缓存项。

### 使用哈希集合 vs 列表
1. 使用哈希集合（set）
- 优点：

 -   查找效率高：哈希集合的查找操作时间复杂度为 O(1)，即你可以非常快速地判断某个字符是否已经存在于集合中。
 -   去重：哈希集合自动去重，如果尝试加入重复的元素，它会直接忽略，保证集合内的元素唯一性。
- 缺点：

 -   空间开销：哈希集合可能会比列表占用更多的内存，因为哈希集合底层是通过哈希表实现的，涉及更多的空间和计算来管理元素。
2. 使用列表（list）
- 优点：

 -   简洁：列表是一种简单的数据结构，Python 中的列表是动态数组，支持直接的元素操作，使用起来相对简单。
- 缺点：

 -   查找效率低：在列表中查找元素的时间复杂度是 O(n)。每次判断一个字符是否已经存在时，都需要遍历整个列表，这会使得时间复杂度较高，尤其是当窗口较大时。
 -   删除效率低：如果窗口中的字符重复，需要从列表中移除某个字符，这个操作的时间复杂度是 O(n)，在最坏的情况下，可能需要遍历整个列表来查找并删除该元素。

- 时间复杂度：
- 哈希集合：每次判断和更新的操作（添加、删除、查找）都可以在 O(1) 时间内完成。因此，整体时间复杂度是 O(n)，其中 n 是字符串的长度。

- 列表：查找元素和删除元素需要遍历整个列表，因此在最坏的情况下，时间复杂度为 O(n²)，因为每次右指针 end 移动时，可能需要遍历列表进行查找和删除操作。

- 总结：
- 哈希集合（set） 是更优的选择，因为它可以在 O(1) 时间内处理查找和删除操作。使用列表会导致查找和删除操作变慢，从而降低程序的效率。
- 列表 虽然可以实现相同的功能，但由于查找和删除操作的效率较低，所以在较大的输入数据下可能会导致性能问题。除非你的数据规模较小或字符集非常有限，否则推荐使用哈希集合来确保高效性。



### reduce()
计算一个列表中所有字典元素的"count"值的总和

问题分析：

    初始迭代：reduce 的第一次调用会将列表的前两个元素作为 x 和 y，此时 x 是字典，x.get("count") 有效。
    
    后续迭代：从第二次迭代开始，x 的类型变为前一步返回的数值（如 x.get("count") + y.get("count") 的结果），而数值没有 .get() 方法，导致抛出 AttributeError。

例如，对于列表 [{"count":1}, {"count":2}, {"count":3}]：

- 第一次迭代：x=1, y=2 → 结果 3。
- 第二次迭代：x=3（数值），尝试 3.get("count") → 报错。
```
from functools import reduce
sub_count = reduce(
    lambda acc, elem: acc + elem.get("count", 0),  # 累加逻辑
    sub_value,  # 输入列表
    0  # 初始值（关键！）
)
```

### 装饰器
```
def my_decorator(func):
    def wrapper():
        print("装饰器：函数调用前")  # 添加被装饰函数执行前的操作
        func()                      # 调用原始函数
        print("装饰器：函数调用后")  # 添加被装饰函数执行后的操作
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")
```

可以使用 next(iter(...)) 来更高效地获取字典的第一个键，避免将所有键转换为列表。
这样可以避免生成整个键列表，提高效率。

缓冲机制：Python的文件写入操作通常会将数据缓存到内存中，只有在缓存满或显式调用flush()时才会将数据写入磁盘。在多线程环境下，缓存机制可能会导致数据没有及时写入文件。

### for-else
for-else是Python独有且常被初学者忽视的一个结构，非常实用。下面给你详细讲解：

1. 基本结构
```
for item in iterable:
    # 处理逻辑
    if 条件:
        break  # 满足条件则跳出整个for循环
else:
    # 如果循环正常结束（即没有遇到break），才会执行这里的代码
python
```
else语句会在整个for循环没有因为break提前退出时执行。
如果for循环碰到了break，else语句就不会执行。
2. 示例讲解
(1) 查找列表元素
```
lst = [1, 3, 5, 7]
for num in lst:
    if num % 2 == 0:
        print("有偶数", num)
        break
else:
    print("列表中没有偶数")
python
```
如果找到偶数，break跳出循环，else不执行。
如果列表里没有偶数，for循环结束后else才会被执行，输出“列表中没有偶数”。
(2) 用于数据过滤
```
origin = [{"id":1},{"id":None}]
for data in origin:
    if not data["id"]:
        break  # 有不满足条件的直接跳出循环
else:
    print("所有数据都符合要求")
python
```
只要有break，else就不会执行。
只有所有数据正常检查完（没break），才会执行else语句，做收尾逻辑。
3. 适用场景
查找： 循环查找某个条件，没找到时额外做点事。
校验： 一组数据都通过时做某个操作，否则在break时提前处理。
4. 常见误解
else不是和if配对，而是和for/while配对！
while循环也有相同用法。
5. 格式概览
```
for item in iterable:
    if ...:
        break
else:
    # 只有没break，这里才执行
python
```
总结一句： for-else中的else只在循环正常结束（没break）时执行，是一种很优雅解决“查找-未找到/全通过-做什么”问题的语法糖。

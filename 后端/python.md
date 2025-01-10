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
```
```
> 警告 不要 调用 super().__new__()，因为只能找到仅用于查找的 __new__；请改为直接使用该数据类型。
```


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
支持的 \_\_dunder__ 名称

\_\_members__ 是一个 member\_name:member 条目的只读有序映射。 它只在类上可用。

如果指定了 \_\_new__()，它必须创建并返回枚举成员；相应地设定成员的 \_value_ 也是一个很好的主意。 一旦所有成员都创建完成它就不会再被使用。

支持的 \_sunder_ 名称
\_name_ -- 成员的名称

\_value_ -- 成员的值；可以在 \_\_new__ 中设置 / 修改

\_missing_ -- 当未发现某个值时所使用的查找函数；可被重写

\_ignore_ -- 一个名称列表，可以为 list 或 str，它不会被转化为成员，并将从最终类中被移除

\_order_ -- 用于 Python 2/3 代码以确保成员顺序一致（类属性，在类创建期间会被移除）

\_generate\_next\_value\_ -- Functional API 和 auto 用它为枚举成员获取适当的值；可被重写

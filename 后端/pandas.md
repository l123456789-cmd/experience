### 做数据差值
单个列表的对比
```
# 使用series
import pandas as pd
# 构造Series，以x为索引
s1 = pd.Series(dict1['y'], index=dict1['x'])
s2 = pd.Series(dict2['y'], index=dict2['x'])
# 计算差值
diff = s2 - s1
# 找出大于3的项
result = diff[diff > 3]
res = list(result.items())
print(result)
```
```

import pandas as pd
# 构造 DataFrame
df1 = pd.DataFrame(dict1)
df2 = pd.DataFrame(dict2)
# 合并两个DataFrame，确保x一一对应（推荐用 merge，针对更复杂的情况）
df = pd.merge(df1, df2, on='x', suffixes=('_1', '_2'))
# suffixes 参数用于对重名列名自动增加后缀
# 计算 y_2 - y_1 > 3
result = df[(df['y_2'] - df['y_1']) > 3]
# 查看结果
print(result)
```

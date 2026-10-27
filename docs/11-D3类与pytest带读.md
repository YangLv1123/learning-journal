# 11 · D3 带读：类与对象 + pytest（10/28 周三用）

> 用法：今晚 D2 完成后，睡前 15 分钟扫一遍；明天动手前再精读。
> 练习：`exercises/d3_counter/`（补全 Counter 类，跑绿 3 个测试）。

## 为什么要有类（60 秒版）

- 变量 → 装**一个**数；列表/字典 → 装**一组**数；
- 类 → 把"数据 + 操作这些数据的方法"**捆在一起**。
- Counter 的"数据"是内部计数 `_value`，"操作"是 `add / subtract / reset / total`。
- 好处：谁用 Counter 都只能通过这 4 个方法操作，内部细节（怎么存、叫什么名）别人不用管——这就是"封装"。

## 四个概念逐一拆

1. **class 与实例**：`class` 是模具，实例是造出来的东西。
   `c = Counter()` → c 是 Counter 的一个实例。
2. **self**：实例自己。方法的第一个参数永远是 self——
   `c.add(5)` 本质上是 `Counter.add(c, 5)`，Python 自动把 c 传进去。
3. **`__init__`**：造实例时自动执行，负责初始化状态（`self._value = 0`）。
4. **属性 vs 方法**：`total` 用 `@property` 装饰后，变成"长得像属性的方法"——
   调用时**不加括号**：`c.total`，而不是 `c.total()`。面试常问"property 有什么好处"：
   读的时候像属性，但每次读都是实时计算的。

## 对应到 counter.py 的四个方法（思路，不是答案）

| 方法 | 思路 |
|---|---|
| `add(value=1)` | `self._value += value`（注意默认参数，不传就是 +1） |
| `subtract(value=1)` | 同理做减法 |
| `total`（property） | 返回 `self._value` |
| `reset` | `self._value = 0` |

## pytest 三件事（D3 开始每天都要用）

1. **自动发现**：文件名 `test_*.py`、函数名 `test_*`，pytest 自己会找到。
2. **断言就是 assert**：不需要 print 检查。`assert c.total == 6` 不通就红，通就绿。
3. **从测试反推需求**：打开 `test_counter.py`，三个测试函数就是你的需求说明书：
   - `test_add`：要求 `add()` 默认 +1、`add(5)` +5，两次后 total 是 6；
   - `test_subtract`：加 10 减 3 后是 7；
   - `test_reset`：加 7 再 reset 后是 0。

## 自查 3 问（明天日志里回答）

1. 方法里为什么必须写 self？
2. `@property` 的方法和普通方法在**调用方式**上有什么区别？
3. 如果 `__init__` 里不初始化 `_value`，第一次调 `add` 会发生什么？（AttributeError——这就是为什么要有 `__init__`）

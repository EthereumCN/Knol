# Vyper

### NatSpec 元数据

Vyper编程语言人用于编写含有状态变量、函数和事件的结构化文档。

就Vyper而言，用`"""`表示注释，且`"""`独自另起一行。

```text
carrotsEaten: int128
"""
@author Bob Clampett
@notice Number of carrots eaten
@dev Chewing does not count, carrots must pass the throat to be "eaten"
"""
```

```text
@public
@payable
def doesEat(food: string):
    """
    @author Bob Clampett
    @notice Determine if Bugs will accept `food` to eat
    @dev Compares the entire string and does not rely on a hash
    @param food The name of a food to evaluate (in English)
    @return true if Bugs will eat it, false otherwise
    """

    // ...
```

```text
Ate: event({food: string})
"""
@author Bob Clampett
@notice Bugs did eat `food`
@dev Chewing does not count, carrots must pass the throat to be "eaten"
@param food The name of a food that was eaten (in English)
"""
```


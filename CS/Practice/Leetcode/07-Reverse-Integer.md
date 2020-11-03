# 7. Reverse Integer

> Given a 32-bit signed integer, reverse digits of an integer

### Solution

복잡할 거 없이 조건문만 추가하여 작성함

* 시간/공간 복잡도 : O(1)

```python
class Solution:
    def reverse(self, x: int) -> int:
        if x < 0:
            integer = int('-' + str(x)[:0:-1])
            if integer < -2**31:
                integer = 0
        else:
            integer = int(str(x)[::-1])
            if integer > 2**31-1:
                integer = 0
        return integer
```


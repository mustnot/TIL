# unittest

> 나는 천재가 아니다. 그렇기에 완벽해질 수 있는 방법은 오로지 테스트 케이스를 다양하게 작성하는 것 뿐이다.

<br>

unittest(단위테스트)는 모듈 또는 응용 프로그램 내 개별 코드 단위가 작동하는지 확인하기 위한 테스트다. 파이썬 공식문서에 따르면, 단위 테스트 프레임워크는 본래 JUnit으로부터 영감을 받고 다른 언어의 주요 단위 테스트 프레임워크와 비슷한 특징을 갖고 있다.

주요 개념 몇 가지를 확인하고 들어가면, **테스트 픽스쳐** (test fixture)는 1개 또는 그 이상의 테스트를 수행할 때 필요한 준비와 그 관련된 정리 동작에 해당하는데 예를 들어 임시 데이터베이스, 디렉토리, 서버 프로세스 등 테스트를 위한 준비를 말한다. **테스트 케이스** (test case) 어떻게 보면 가장 중요하다고 생각되는 테스트 케이스는 테스트의 개별 단위로 특정한 입력 모음과 특정한 결과의 묶음을 말한다. 즉 특정 입력으로 인한 특정 결과를 케이스라고 생각하면 된다. 이외에도 테스트 묶음, 테스트 실행자이 있지만, 자세하게 다뤄보진 않을 예정이다.

```python
import unittest

class CustomTestCases(unittest.TestCase):
    def test_run(self):
        function()

def function():
    pass


if __name__ == "__main__":
    unittest.main()
```

```bash
$ python3 test.py
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```


위 코드는 아무것도 작성되지 않은 함수를 호출하는 unittest 코드로 실행하면 아래 결과와 같이 1개의 테스트를 실행했고, 0.000s의 시간이 소요되었다고 표시된다. 조금 더 자세하게 코드를 작성해보자

<br>

```python
import unittest

class CustomTestCases(unittest.TestCase):
    def test_run_1(self):
        plus(5, 4)
    
    def test_run_2(self):
        minus(4, 1)

def plus(a, b):
    return a + b

def minus(a, b):
    return a - b


if __name__ == "__main__":
    unittest.main()
```

`plus` 와 `minus` 라는 함수 두 개를 작성하고 각각 `test_run_1`과 `test_run_2`에 넣어놨다. 우선 코드가 실행되는데는 이상 없기 때문에 테스트에는 아무 이상이 없지만, 여기서부터는 그 결과값이 맞는지 앞서 테스트 케이스의 설명을 다시 생각해보면 입력과 결과의 묶음을 테스트 케이스라고 한다는 것을 잊지말고, 결과에 대한 테스트도 진행해야한다.

unittest의 메소드에는 assert가 존재하는데, 여기서 assert는 결과에 대한 테스트에 사용된다. 먼저 종류를 살펴보면 종류는 정말로 많지만 이 중에 내가 생각하는 대표적인 몇 가지만 본다면 다음과 같다.

|          Method          |      Checking      |
| :----------------------: | :----------------: |
|   `assertEqual(a, b)`    |       `a==b`       |
|  `assertNotEqual(a, b)`  |       `a!=b`       |
|     `assertTrue(x)`      | `bool(x) is True`  |
|     `assertFalse(x)`     | `bool(x) is False` |
|    `assertIsNone(x)`     |    `x is None`     |
|   `assertIsNotNone(x)`   |  `x is not None`   |
| `assertIsInstance(a, b)` | `isinstance(a, b)` |

그럼 이 중에서 위에 간단하게 작성한 `plus` 함수와 `minus`함수는 어떠한 `assert` 메소드를 선택해야 검증하기 좋을까 생각해보면 당연하지만 `assertEqual(a, b)` 이다. 그럼 코드를 보면, 다음과 같다.

<br>

```python
import unittest

class CustomTestCases(unittest.TestCase):
    def test_run_1(self):
        self.assertEqual(plus(5, 4), 9)
        self.assertEqual(plus(5, 3), 9)

    def test_run_2(self):
        self.assertEqual(minus(4, 1), 3)
        self.assertEqual(minus(4, 5), 1)

def plus(a, b):
    return a + b

def minus(a, b):
    return a - b


if __name__ == "__main__":
    unittest.main()
```

```bash
$ python3 test.py
FF
======================================================================
FAIL: test_run_1 (__main__.CustomTestCases)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "test.py", line 6, in test_run_1
    self.assertEqual(plus(5, 3), 9)
AssertionError: 8 != 9

======================================================================
FAIL: test_run_2 (__main__.CustomTestCases)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "test.py", line 10, in test_run_2
    self.assertEqual(minus(4, 5), 1)
AssertionError: -1 != 1

----------------------------------------------------------------------
Ran 2 tests in 0.000s

FAILED (failures=2)
```

작성한 코드를 보면 당연하겠지만, 2번의 실패가 나타난다. 그럼 unittest 결과에서는 이런 테스트 결과를 어떻게 보여줄까를 보면, `test_run_1`이 실패했다는 문구와 함께 몇 번째 라인 그리고 어떤 코드에서 에러가 났고 결과는 어떻게 다른지 상세하게 나온다. 이처럼 단순히 테스트 케이스를 코드를 작성하기만 하더라도 내가 작성한 코드가 잘못되었는지 바로 식별할 수 있다.

내가 테스트를 작성해놓고 가장 편했던 것 중에 하나는 유지 보수 할 때인데, 내가 작성했던 기능을 유지보수하게 되면서 기존에 정상적으로 실행되던 코드가 중간에 잘못 입력되었거나 결과에 영향을 주게 될 경우 기존의 테스트 케이스가 안 맞는 경우가 있는데 이런 경우를 바로 알아차릴 수 있어서 좋았다. (모든 케이스를 다 기억하고 있는게 아니기 때문이다. 난 천재가 아니다.)

<br>

가장 단순하게 작성해서 unittest에 대해 알아봤지만, 실제로 테스트 코드를 작성할 때에는 이보다 정말 수 천줄 이상 되는 코드가 될 수도 있고, 테스트 케이스를 무사히 거치더라도 반례가 생기기 마련이기 때문에 작성하기 전에 최대한 디테일하고 다양한 인풋과 결과에 대해 작성할 필요가 있다. 다시 한번 말하면, 난 천재가 아니다.



## Reference

* [unittest - wikidocs](https://wikidocs.net/16107)
* [unittest - python docs](
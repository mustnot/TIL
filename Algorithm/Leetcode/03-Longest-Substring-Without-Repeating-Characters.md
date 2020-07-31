## 03. Longest Substring Without Repeating Characters

>  Given a string, find the length of the **longest substring** without repeating characters.

<br>

### Brute Force

확실히 Brute Force 방식은 실행 시간 제약이 있는 경우 정답이 될 수가 없다. 코드는 `index` 를 하나하나 증가시켜 `substring` 을 만드는 방식으로 탐색하는데, 길이가 긴 텍스트에서 타임아웃이 걸려 정답이 되지 않았다.

* 시간 복잡도 : 반복문 2개로 O(n^2)
* 공간 복잡도 : O(1)

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        ans = 0
        for i in range(0, len(s)):
            for j in range(i+ans, len(s)):
                substring = s[i:j+1]
                if ans < len(substring):
                    if self.isAllUniqueString(substring):
                        ans = len(substring)
        return ans

    def isAllUniqueString(self, s: str):
        if len(s) == len(set(s)):
            return True
        return False
```

<br>

좋은 코드는 아니지만, Brute Force 방법으로 정답 처리가 되는 방법이 있었는데, 바로 정답의 최대값을 지정하는 것이다. `Time Limit Exceeded` 가 난 `string`을 보니깐 아래와 같이 95자로 된 단어였는데 `ans == 95` 인 경우 `break`를 걸었더니 정답 처리가 되었다. 사실 제약하지 않고 해결하는게 정답이다. 아래 단어 외에도 한자 혹은 중국어, 이모지 같은 수많은 단어들이 있기 때문이다. 

```python
abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789!\"#$%&'()*+,-./:;<=>?@[\\]^_`{|}~ 
```

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        ans = 0
        for i in range(0, len(s)):
            for j in range(i+ans, len(s)):
                substring = s[i:j+1]
                if ans < len(substring):
                    if self.isUniqueString(substring):
                        ans = len(substring)
            # 최대 길이 제약 추가
            if ans == 95:
                break
        return ans

    def isUniqueString(self, s: str):
        if len(s) == len(set(s)):
            return True
        return False
```

<br>

<br>

### Using Dictionary (Hash)

다른 방법인데, 위에서는 `set`을 단순히 `substring`의 모든 단어가 `unique`한지만 검사했는데, `set`외에도 `dict`를 사용하면 (`hash`와 유사) 동일한 키값은 허용되지 않기 때문에 하나의 키값만 포함되어서 특정 문자열이 어떤 인덱스를 가지고 있는지 확인이 가능하다.

* 시간 복잡도 : 반복문 하나만 사용되어서 O(n)
* 공간 복잡도 : 탐색한 길이만큼 `used_string` 에 할당되어서 O(n)

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        used_string = {}
        ans = start = 0
        for i, c in enumerate(s):
            if c in used_string and start <= used_string[c]:
                start = used_string[c] + 1
            else:
                ans = max(ans, i - start + 1)
            used_string[c] = i
        return ans
```


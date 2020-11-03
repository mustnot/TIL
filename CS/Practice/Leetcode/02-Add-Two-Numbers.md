## 02. Add Two Numbers

>  You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order** and each of their nodes contain a single digit. Add the two numbers and return it as a linked list. (You may assume the two numbers do not contain any leading zero, except the number 0 itself.)
>

두 개의 음수가 아닌 정수를 나타내는 비어 있지 않은 연결 리스트가 주어진다. 자릿수는 역순으로 저장되며 각 노드는 한 자릿수를 포함한다. 두 숫자를 추가한 후 링크된 목록으로 반환하십시오. (0이라는 숫자 자체를 제외하고 두 숫자에 선행 0이 포함되어 있지 않다고 가정할 수 있다.)



### Recursive Function

> 그리고 나는 그 동안 알고리즘 문제를 풀면서 잘 몰랐지만, 파라미터에 새로운 변수를 추가할 수 있다는걸 오늘 알았다.. (바보다)

처음에는 `ListNode` 클래스를 다시 만들어서 풀어야하는지 많은 삽질을 진행했다. 실제로도 `ListNode`에 `add_val` 이라는 메서드를 만들어서 값이 더해지고 그 값이 10 이상일 경우에 다음 노드의 값에 1을 더하던가 혹은 새로 생성해서 1이라는 값을 주는 방법으로 진행했다. 실제로는 재귀함수를 이용하면 되는 것이였는데, 노드의 다음 부분을 재귀함수로 계속해서 생성해나가면 마지막에 더 이상 더 할 것이 없을 때 다시 돌아와 `return` 하는 방법으로 코드를 짰다.

* 시간 복잡도 : `l1`, `l2`가 얼마나 많은지에 따라 달라지고 가장 많이 연결된 노드에 따라 복잡도가 늘어난다. 즉 가장 긴 리스트에 영향을 받아 $$ O(max(l1, l2)) $$ 이다.
* 공간 복잡도 : 공간 복잡도 역시 시간 복잡도와 동일하게 가장 긴 리스트에 영향을 받아 새롭게 리스트 노드가 생성되기 때문에 $$ O(max(l1, l2)) $$ 이다. (만약 마지막 더한 값이 10이상일 경우에는 $$ O(max(l1, l2)) + 1 $$ 이다.

```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
    

class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode, l3=None):
        if l3 is None:
            l3 = ListNode(0)
        
        l3.val += l1.val + l2.val
        if l3.val >= 10:
            l3.val -= 10
            l3.next = ListNode(1)
        
        
        if l1.next or l2.next:
            if l1.next is None:
                l1.next = ListNode(0)
            if l2.next is None:
                l2.next = ListNode(0)
            l3.next = self.addTwoNumbers(l1.next, l2.next, l3.next)
        
        return l3
```


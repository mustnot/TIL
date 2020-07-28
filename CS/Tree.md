# Tree

- Node와 Branch를 이용해서, 사이클을 이루지 않도록 구성한 데이터 구조
- 트리 중 이진 트리 (Binary Tree) 형태의 구조로 탐색(검색) 알고리즘 구현을 위해 많이 사용됨

<p align="center">
  <img width="460" height="300" src="https://user-images.githubusercontent.com/52126612/82443386-5f440f80-9adc-11ea-9b10-2b5041e99981.png">
</p>

<br>

## ✔용어 정리

1. **Node :** 트리에서 데이터를 저장하는 기본 요소 (데이터와 다른 연결된 노드에 대한 Branch 정보 포함)
2. **Root Node :** 트리 맨 위에 있는 노드
3. **Level :** 최상위 노드를 Level 0으로 하였을 때, 하위 Branch로 연결된 노드의 깊이를 나타냄
4. **Parent Node:** 어떤 노드의 다음 레벨에 연결된 노드
5. **Child Node:** 어떤 노드의 상위 레벨에 연결된 노드
6. **Leaf Node (Terminal Node) :** Child Node가 하나도 없는 노드
7. **Sibling (Brother Node) :** 동일한 Parent Node를 가진 노드
8. **Depth :** 트리에서 Node가 가질 수 있는 최대 Level

<br>

## 1. Binary Search Tree (이진 트리와 이진 탐색 트리)

- 이진 트리 : 노드의 최대 Branch가 2인 트리
- 이진 탐색 트리 (Binary Search Tree, BST) : 이진 트리에 다음과 같은 추가적인 조건이 있는 트리
    - 왼쪽은 노드보다 작은 값, 오른쪽은 노드보다 큰 값

<p align="center">
  <img width="460" height="200" src="https://user-images.githubusercontent.com/52126612/82443392-60753c80-9adc-11ea-9410-bcb8cf8507d0.png">
</p>

- 주요 용도 : 데이터 검색 (탐색)
- 장점 : 탐색 속도를 개선할 수 있음
- 단점 : 평균 시간 복잡도는 O(logn)이지만, 최악의 경우 리스트 등과 동일한 성능을 보여줌

<br>

## 2. 링크드 리스트로 이진 탐색 트리 구현하기

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

class NodeMgmt:
    def __init__(self, head):
        self.head = head

    def insert(self, value):
        self.current_node = self.head
        while True:
            if value < self.current_node.value:
                if self.current_node.left != None:
                    self.current_node = self.current_node.left
                else:
                    self.current_node.left = Node(value)
                    break
            else:
                if self.current_node.right != None:
                    self.current_node = self.current_node.right
                else:
                    self.current_node.right = Node(value)
                    break

    def search(self, value):
        self.current_node = self.head
        while self.current_node:
            if self.current_node.value == value:
                return True
            elif value < self.current_node.value:
                self.current_node = self.current_node.left
            else:
                self.current_node = self.current_node.right
        return False
```
<br>

## 3. 이진 탐색 트리 삭제

### 0. 삭제할 Node 탐색

```python
def delete(self, value):
    searched = False
    self.current_node, self.parent = self.head, self.head
    while self.current_node:
        if self.current_node.value == value:
            searched = True
        elif value < self.current_node.value:
            self.parent = self.current_node
            self.current_node = self.current_node.left
        else:
            self.parent = self.current_node
            self.current_node = self.current_node.right

    if searched == False:
        return False1. Leaf Node 삭제
```

<p align="center">
  <img width="480" height="220" src="https://user-images.githubusercontent.com/52126612/82443394-610dd300-9adc-11ea-8f66-1a254ca79a0d.png">
</p>

- Leaf Node : Child Node 가 없는 Node
- 삭제할 Node의 Parent Node가 삭제할 Node를 가리키지 않도록 한다.

```python
if self.current_node.left == self.current_node.right:
    if self.parent.left == self.current_node:
        self.parent.left = None
    else:
        self.parent.right = None
    del self.current_node
```

### 2. Child Node가 하나인 Node 삭제

<p align="center">
  <img width="500" height="200" src="https://user-images.githubusercontent.com/52126612/82443396-61a66980-9adc-11ea-852d-f27a13e8c26b.png">
</p>


- 삭제할 Node의 Parent Node가 삭제할 Node의 Child Node를 가리키도록 한다.

```python
if self.current_node.left and self.current_node.right == None:
    if value < self.parent.value:
        self.parent.left = self.current_node.left
    else:
        self.parent.right = self.current_node.left
else:
    if value > self.parent.value:
        self.parent.right = self.current_node.right
    else:
        self.parent.left = self.current_node.right
```

### 3. Child Node가 두 개인 Node 삭제

<p align="center">
  <img width="500" height="200" src="https://user-images.githubusercontent.com/52126612/82443397-61a66980-9adc-11ea-8d3e-e8b7fd74b82d.png">
</p>

- 삭제할 Node의 오른쪽 자식 중, 가장 작은 값을 삭제할 Node의 Parent Node가 가리키도록 한다.
    1. 삭제할 Node의 오른쪽 자식 선택
    2. 오른쪽 자식의 가장 왼쪽에 있는 Node를 선택
    3. 해당 Node를 삭제할 Node의 Parent Node의 왼쪽 Branch가 가리키게 함
    4. 해당 Node의 왼쪽 Branch가 삭제할 Node의 왼쪽 Child Node를 가리키게 함
    5. 해당 Node의 오른쪽 Branch가 삭제할 Node의 왼쪽 Child Node를 가리키게 함
    6. 만약 해당 Node가 오른쪽 Child Node를 가지고 있었을 경우에는, 해당 Node의 본래 Parent Node의 왼쪽 Branch가 해당 오른쪽 Child Node를 가리키게 함
- 삭제의 Node의 왼쪽 자식 중, 가장 큰 값을 삭제할 Node의 Parent Node가 가리키도록 한다

```python
if self.current_node.left != None and self.current_node.right != None: # case3
    if value < self.parent.value: # case3-1
        self.change_node = self.current_node.right
        self.change_node_parent = self.current_node.right
        while self.change_node.left != None:
            self.change_node_parent = self.change_node
            self.change_node = self.change_node.left
        if self.change_node.right != None:
            self.change_node_parent.left = self.change_node.right
        else:
            self.change_node_parent.left = None
        self.parent.left = self.change_node
        self.change_node.right = self.current_node.right
        self.change_node.left = self.change_node.left

    else:
        self.change_node = self.current_node.right
        self.change_node_parent = self.current_node.right
        while self.change_node.left != None:
            self.change_node_parent = self.change_node
            self.change_node = self.change_node.left
        if self.change_node.right != None:
            self.change_node_parent.left = self.change_node.right
        else:
            self.change_node_parent.left = None
        self.parent.right = self.change_node
        self.change_node.left = self.current_node.left
        self.change_node.right = self.current_node.right
```
<br>

## 4. 이진 탐색 트리의 시간 복잡도와 단점

- depth (트리의 높이)를 h라고 표기한다면, O(h)
- n개의 노드를 가진다면, h=logn 에 가까우므로, 시간 복잡도는 O(logn)
    - log는 밑이 10이 아닌 2를 의미로 즉 50%의 실행 시간을 단축시킬 수 있음

<br>

## 5. 전체 파이썬 코드

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

class NodeMgmt:
    def __init__(self, head):
        self.head = head

    def insert(self, value):
        self.current_node = self.head
        while True:
            if value < self.current_node.value:
                if self.current_node.left != None:
                    self.current_node = self.current_node.left
                else:
                    self.current_node.left = Node(value)
                    break
            else:
                if self.current_node.right != None:
                    self.current_node = self.current_node.right
                else:
                    self.current_node.right = Node(value)
                    break
    
    def search(self, value):
        self.current_node = self.head
        while self.current_node:
            if self.current_node.value == value:
                return True
            elif value < self.current_node.value:
                self.current_node = self.current_node.left
            else:
                self.current_node = self.current_node.right
        return False        
    
    def delete(self, value):
        # 삭제할 노드 탐색
        searched = False
        self.current_node = self.head
        self.parent = self.head
        while self.current_node:
            if self.current_node.value == value:
                searched = True
                break
            elif value < self.current_node.value:
                self.parent = self.current_node
                self.current_node = self.current_node.left
            else:
                self.parent = self.current_node
                self.current_node = self.current_node.right

        if searched == False:
            return False

        # case 1 : Child Node가 하나인 Node 삭제
        if  self.current_node.left == None and self.current_node.right == None:
            if value < self.parent.value:
                self.parent.left = None
            else:
                self.parent.right = None
        
        # case 2 : Child Node가 하나인 Node 삭제
        elif self.current_node.left != None and self.current_node.right == None:
            if value < self.parent.value:
                self.parent.left = self.current_node.left
            else:
                self.parent.right = self.current_node.left
        elif self.current_node.left == None and self.current_node.right != None:
            if value < self.parent.value:
                self.parent.left = self.current_node.right
            else:
                self.parent.right = self.current_node.right        
        
        # case 3 : Child Node가 두 개인 Node 삭제
        elif self.current_node.left != None and self.current_node.right != None:
            # case 3-1 : 삭제할 Node의 오른쪽 자식 중, 가장 작은 값을 삭제할 Node의 Parent Node가 가리키도록 한다.
            if value < self.parent.value:
                self.change_node = self.current_node.right
                self.change_node_parent = self.current_node.right
                while self.change_node.left != None:
                    self.change_node_parent = self.change_node
                    self.change_node = self.change_node.left
                if self.change_node.right != None:
                    self.change_node_parent.left = self.change_node.right
                else:
                    self.change_node_parent.left = None
                self.parent.left = self.change_node
                self.change_node.right = self.current_node.right
                self.change_node.left = self.change_node.left
            # case 3-2 : 삭제할 Node의 왼쪽 자식 중, 가장 큰 값을 삭제할 Node의 Parent Node가 가리키도록 한다.
            else:
                self.change_node = self.current_node.right
                self.change_node_parent = self.current_node.right
                while self.change_node.left != None:
                    self.change_node_parent = self.change_node
                    self.change_node = self.change_node.left
                if self.change_node.right != None:
                    self.change_node_parent.left = self.change_node.right
                else:
                    self.change_node_parent.left = None
                self.parent.right = self.change_node
                self.change_node.right = self.current_node.right
                self.change_node.left = self.current_node.left

        return True
```
# Linked List (링크드 리스트)

![Singly_linked_list](https://user-images.githubusercontent.com/52126612/82140775-1be36a00-986c-11ea-9505-c14cdd7036d4.png)

- 연결 리스트라고도 함
- 배열은 순차적으로 연결된 공간에 데이터를 나열하는 데이터 구조
- 링크르 리스트는 떨어진 곳에 존재하는 데이터를 화살표로 연결해서 관리하는 데이터 구조
- 링크드 리스트 기본 구조와 용어
    - 노드(Node): 데이터 저장 단위 (데이터값, 포인터)로 구성
    - 포인터(Pointer): 각 노드 안에서 다음이나 이전의 노드와의 연결 정보를 가지고 있는 공간

<br>

## 1. 간단한 링크드 리스트 예

### Node 구현

- 보통 파이썬에서 링크르 리스트 구현시, 클래스를 활용함
    - 파이썬 객체지향 문법 이해 필요

```python
class Node:
    def __init__(self, data, next=None):
        self.data = data
        self.next = next
```

### Node와 Node 연결하기 (포인터 활용)

```python
node1 = Node(1)
node2 = Node(2)
node1.next = Node2
head = node1
```


### 링크드 리스트로 데이터 추가하기

```python
def add(data):
    node = head
    while node.next:
        node = node.next
    node.next = Node(data)
```

```python
node1 = Node(1)
head = node1
for index in range(2, 10):
    add(index)
```

<br>

## 2. 링크드 리스트의 장단점 (C언어에서의 배열과 링크드 리스트)

- 장점
    - 데이터 공간을 미리 할당하지 않아도 됨
        - 배열은 미리 데이터 공간을 할당해야 함
- 단점
    - 연결을 위한 별도 데이터 공간이 필요하므로, 저장공간 효율이 높지 않음
    - 연결 정보를 찾는 시간이 필요하므로 접근 속도가 느림
    - 중간 데이터 삭제시, 앞뒤 데이터 연결을 재구성해야 하는 부가적인 작업 필요

<br>

## 3. 링크드 리스트의 복잡한 기능

### 링크드 리스트 데이터 사이에 데이터를 추가

- 연결하고자 하는 연결 정보를 찾아 해당 연결 정보 앞 뒤에 데이터를 추가해야함


### 특정 노드를 삭제

- 특정 노드를 삭제할 경우 앞 뒤 연결정보를 이어줘야함

<br>

## 4. 파이썬 객체 지향 프로그래밍으로 링크드 리스트 구현하기

```python
class Node:
    def __init__(self, data, next=None):
        self.data = data
        self.next = next
    
class NodeMgmt:
    def __init__(self, data):
        self.head = Node(data)
        
    def add(self, data):
        if self.head == '':                   # head가 없을 경우, head에 추가
            self.head = Node(data)
        else:
            node = self.head
            while node.next:
                node = node.next
            node.next = Node(data)
        
    def desc(self):
        node = self.head
        while node:
            print (node.data)
            node = node.next
    
    def delete(self, data):
        if self.head == '':
            print ("해당 값을 가진 노드가 없습니다.")
            return
        
        if self.head.data == data:
            temp = self.head
            self.head = self.head.next
            del temp
        else:
            node = self.head
            while node.next:
                if node.next.data == data:
                    temp = node.next
                    node.next = node.next.next
                    del temp
                    return
                else:
                    node = node.next

    def search_node(self, data):
        node = self.head
        while node:
            if node.data == data:
                return node
            else:
                node = node.next
```

<br>

## 5. 다양한 링크드 리스트 구조

### 더블 링크드 리스트 (Doubly Linked List) 기본 구조
![Doubly_linked_list](https://user-images.githubusercontent.com/52126612/82140769-1128d500-986c-11ea-866f-956c023c5d90.png)


- 이중 연결 리스트라고 함
- 장점: 양방향으로 연결되어 있어서 노드 탐색이 양쪽으로 모두 가능

```python
class Node:
    def __init__(self, data, prev=None, next=None)
        self.data = data
        self.prev = prev
        self.next = next

class NodeMgmt:
    def __init__(self, data):
        self.head = Node(data)
        self.tail = self.head

    def insert(self, data):
        if self.head == None:
            self.head = Node(data)
            self.tail = self.head
        else:
            node = self.head
            while node.next:
                node = node.next
            new = Node(data, prev=node)
            node.next = new
            self.tail = new
        
    def desc(self):
        node = self.head
        while node:
            print(node.data)
            node = node.next

```


### 연습 : 위 코드에서 노드 데이터가 특정 숫자인 노드 앞과 뒤에 데이터를 추가하는 함수 만들고, 테스트해보기

```python
class Node:
    def __init__(self, data, prev=None, next=None):
        self.data = data
        self.prev = prev
        self.next = next

class NodeMgmt:
    def __init__(self, data):
        self.head = Node(data)
        self.tail = self.head

    def insert(self, data):
        if self.head == None:
            self.head = Node(data)
            self.tail = self.head
        else:
            node = self.head
            while node.next:
                node = node.next
            new = Node(data, prev=node)
            node.next = new
            self.tail = new
        
    def desc(self):
        node = self.head
        while node:
            print(node.data)
            node = node.next

    def search_node(self, data):
        node = self.head
        while node:
            if node.data == data:
                return node
            node = node.next

    def insert_prev(self, data, prev_data):
        node = self.search_node(data)
        new = Node(prev_data)
        if self.head == node:
            self.head = new
            new.next = node
            node.prev = new
        else:
            node.prev.next = new
            new.prev = node.prev
            new.next = node
            node.prev = new
                
    def insert_next(self, data, next_data):
        node = self.search_node(data)
        new = Node(next_data)
        if self.tail == node:
            self.tail = new
            new.prev = node
            node.next = new
        else:
            node.next.prev = new
            new.next = node.next
            new.prev = node
            node.next = new
```
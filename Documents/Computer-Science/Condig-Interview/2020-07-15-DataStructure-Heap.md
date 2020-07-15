# Data Structure - Heap

> 자료 구조에 대해 한번 정리하고 넘어간다. 어느 정도 안다고 생각하더라도 다시 정리하면 내가 기억했던 내용과 다를 수 있기 때문에 리마인드하는 마음으로 정리 2

<br>

## Heap

**힙**(Heap)은 최댓값 혹은 최솟값을 찾아내는 연산을 빠르게 하기 위해 고안된 완전이진트리를 기본으로 한 자료 구조로서 힙 속성을 만족한다. 여기서 힙 속성은 "A가 B의 부모 노드(parent node)이면, A의 키(Key)값과 B의 키값 사이에는 대소관계가 성립한다."인데, 여기서 대소관계가 성립하기 때문에 힙에도 두 가지 종류가 있다.

먼저, 부모 노드의 키값이 자식 노드의 키 값보다 항상 큰 힙을 **최대 힙**, 그 반대의 경우 **최소 힙**이라 부른다. 그렇기에 가장 최상단에 있어야하는 루트의 키 값은 항상 가장 큰 값으로 최댓값 혹은 가장 작은 값을 가진 최솟값이 된다.

힙은 **우선순위 큐**를 위해 만들어진 자료 구조인데, 우선순위 큐란, 우선 순위의 개념을 큐에 도입한 자료 구조로 데이터가 큐 안에 들어오게 되었을 때, 나가는 경우에는 순차적인 순서 대표적인 LIFO, FIFO가 아닌 이미 정해진 우선 순위 순서로 나오게 된다.

<br>

### Rules

**힙**은 표준적으로는 **배열**로 만드는데, 구현을 쉽게 하기 위해 배열의 첫 번째 인덱스인 0은 사용되지 않는다. 배열을 사용하는 가장 큰 이유는 특정 데이터가 들어오더라도 기존에 들어온 데이터의 위치는 변하지 않는다는 점으로 사용된다. **힙**은 다음과 같은 관계로 구성된다.

```
왼쪽 자식 index = (부모 index) * 2
오른쪽 자식 index = (부모 index) * 2 + 1
부모 index = (자식 index) // 2
```

<br>

### Process

**힙**에서 데이터의 **삽입**은 일단 힙의 마지막 노드에 삽입하고, 새로운 노드를 부모 노드들과 교환하면서 위치를 변경한다. **삭제**는 **최대 힙**의 경우 가장 큰 값 가장 맨 앞에 있는 루트 노드가 삭제된다. 삭제된 루트 노드는 힙의 마지막 노드를 가져오고 힙을 재구성한다.

<br>

### Code (Max Heap)

**1. 삽입 구현**

* [Heap - Tech-Interview-for-developer](https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer%20Science/Data%20Structure/Heap.md)를 참조한 `Java` 코드

```java
void insert_max_heap(int x) {
    
    maxHeap[++heapSize] = x; 
    // 힙 크기를 하나 증가하고, 마지막 노드에 x를 넣음
    
    for( int i = heapSize; i > 1; i /= 2) {
        
        // 마지막 노드가 자신의 부모 노드보다 크면 swap
        if(maxHeap[i/2] < maxHeap[i]) {
            swap(i/2, i);
        } else {
            break;
        }       
    }
}
```

* 위 코드를 `Python`으로 구현

```python
def insert(value):
    heap.append(value)
    index = len(heap) - 1

    parent_index = int(index / 2)
    while parent_index > 0:
        if heap[parent_index] < value:
            heap[parent_index], heap[index] = heap[index], heap[parent_index]
            index = parent_index
            parent_index = int(parent_index / 2)
        else:
            break
    return heap
```



<br>

**2.** **삭제 구현**

* [Heap - Tech-Interview-for-developer](https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer%20Science/Data%20Structure/Heap.md)를 참조한 `Java` 코드

```java
int delete_max_heap() {
    
    if(heapSize == 0) // 배열이 비어있으면 리턴
        return 0;
    
    int item = maxHeap[1]; // 루트 노드의 값을 저장
    maxHeap[1] = maxHeap[heapSize]; // 마지막 노드 값을 루트로 이동
    maxHeap[heapSize--] = 0; // 힙 크기를 하나 줄이고 마지막 노드 0 초기화
    
    for(int i = 1; i*2 <= heapSize;) {
        
        // 마지막 노드가 왼쪽 노드와 오른쪽 노드보다 크면 끝
        if(maxHeap[i] > maxHeap[i*2] && maxHeap[i] > maxHeap[i*2+1]) {
            break;
        }
        
        // 왼쪽 노드가 더 큰 경우, swap
        else if (maxHeap[i*2] > maxHeap[i*2+1]) {
            swap(i, i*2);
            i = i*2;
        }
        
        // 오른쪽 노드가 더 큰 경우
        else {
            swap(i, i*2+1);
            i = i*2+1;
        }
    }
    
    return item;
    
}
```

* `Python`으로 구현

```python
def delete():
    if len(heap) < 1:
        return False
    heap[1], heap[-1] = heap[-1], heap[1]
    item = heap.pop()

    index = 1
    while index < len(heap):
        parent_index = int(index/2)
        while parent_index > 0:
            if heap[parent_index] < heap[index]:
                heap[parent_index], heap[index] = heap[index], heap[parent_index]
                index = parent_index
                parent_index = int(index/2)
            else:
                break
        index *= 2
    return item
```



<br>

### Memo

직접 코드를 짜본다고 시간을 많이 잡아먹은 것 같다. 확실히 다른 언어로 쓰였지만 코드를 따라 작성하는 건 꽤나 어려운 것 같다. 
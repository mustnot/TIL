# 깊이 우선 탐색(DFS) 과 너비 우선 탐색(BFS)

> ✔ 예제를 찾아서 복습할 필요성이 있음. 꼭 한번 복습하자 !

- 대표적인 **그래프 탐색** 알고리즘

<p align="center">
  <img width="460" height="250" src="https://user-images.githubusercontent.com/52126612/83351585-31828480-a380-11ea-9c7a-693cd2af0d34.png">
</p>


- **너비 우선 탐색 (Breadth-First Search) :** 정점들과 같은 레벨에 있는 노드 (형제 노드)를 먼저 탐색하는 방식
    - A - B - C - D - G - H - I - E - F - J
    - 한 단계 씩 내려가며 해당 노드와 **같은 레벨**에 있는 노드를 먼저 순회하며 탐색
- **깊이 우선 탐색 (Depth-First Search) :** 정점의 자식들을 먼저 탐색하는 방식
    - A - B - D - E - F - C - G - H - I - J
    - 한 노드의 자식을 타고 **끝까지 순회**한 후 다시 돌아와 다른 형제들의 자식을 타고 내려가며 탐색

<br>

### 파이썬으로 그래프를 표현하는 방법

```python
graph = dict()

graph['A'] = ['B', 'C']
graph['B'] = ['A', 'D']
graph['C'] = ['A', 'G', 'H', 'I']
graph['D'] = ['B', 'E', 'F']
graph['E'] = ['D']
graph['F'] = ['D']
graph['G'] = ['C']
graph['H'] = ['C']
graph['I'] = ['C', 'J']
graph['J'] = ['I']
```

<br>

## 깊이 우선 탐색 (DFS : Depth-First Search) 구현

> ✔ **DFS**는 **스택과 큐**를 활용하는 반면, **BFS**는 **두 개의 큐**를 활용하는 점이 차이점

- 자료 구조 중 **스택과 큐**를 활용하여 구현
- 큐와 스택 구현은 기본 내장 라이브러리를 활용할 수 있지만, 여기서는 간단하게 리스트를 활용한다.

### 일반적인 DFS 시간 복잡도

- 노드 수: V / 간선 수: E
- 아래 코드에서는 while need_visit 은 V + E 번 만큼 수행함
- 시간 복잡도: O(V + E)

```python
# study code
def dfs(graph, start_node):
    visited, need_visit = [], [start_node]
    
    while need_visit:
        node = need_visit.pop()
        if node not in visited:
            visited.append(node)
            need_visit.extend(graph[node])
    return visited
```

<br>

## 너비 우선 탐색 (BFS : Breadth-First Search) 구현

- 자료 구조 중 **큐**를 활용함
- visited, need_visit를 큐를 활용하여 만들어 활용
    - 간단히 파이썬 리스트 활용


<p align="center">
  <img width="460" height="250" src="https://user-images.githubusercontent.com/52126612/83351586-32b3b180-a380-11ea-9c91-714513be4d39.png">
</p>

```python
# study code
def bfs(graph, start_node):
    visited = list()
    need_visit = list()
    
    need_visit.append(start_node)
    
    while need_visit:
        node = need_visit.pop(0)
        if node not in visited:
            visited.append(node)
            need_visit.extend(graph[node])
    
    return visited
```

```python
# my code (using queue)
import queue

def dfs(graph, start_node):
    visited, need_visit = [], queue.Queue()
    need_visit.put(start_node)
    
    while need_visit.qsize():
        node = need_visit.get()
        if node not in visited:
            visited.append(node)
            for children in graph[node]:
                need_visit.put(node)
    return visited
```


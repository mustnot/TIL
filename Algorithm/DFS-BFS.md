> 먼저 기억해야할 내용

- BFS (Breadth-First Search) : 너비 우선 탐색으로 정점들과 같은 레벨에 있는 노드 (형제 노드)를 먼저 탐색하는 방식으로 한 단계 씩 내려가며 해당 노드와 같은 레벨에 있는 노드를 먼저 순회하며 탐색한다.
- DFS (Depth-First Search) : 깊이 우선 탐색으로 정점의 자식들을 먼저 탐색하는 방식으로 한 노드의 자식을 끝까지 순회한 후 다시 돌아와 다른 형제들의 자식을 타고 내려가며 탐색

파이썬으로 그래프는 다음과 같이 표현한다.

```python
graph = {}

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

## Q. 단어 변환

```python
from collections import defaultdict

def diff_words(word1, word2):
    return sum([1 if w1 != w2 else 0 for w1, w2 in zip(word1, word2)])

def solution(begin, target, words):
    answer = 0
    if target not in words:
        return 0

    visited, need_visited = [], [begin]

    while need_visited:
        answer += 1
        begin = need_visited.pop()
        visited.append(begin)
        for word in words:
            if diff_words(begin, word) == 1:
                need_visited.append(word)
                if word == target:
                    return answer

    return answer
```

<br>

## 타겟 넘버

```python
import copy

def solution(numbers, target):
    computations = ['+', '-']

    formulas = [0]
    for number in numbers:
        new_formulas = []
        for formula in formulas:
            new_formulas.append(formula+number)
            new_formulas.append(formula-number)
        formulas = copy.deepcopy(new_formulas)

    return formulas.count(target)
```

<br>

## DFS와 BFS

그래프를 DFS로 탐색한 결과와 BFS로 탐색한 결과를 출력하는 프로그램을 작성하시오. 단, 방문할 수 있는 정점이 여러 개인 경우에는 정점 번호가 작은 것을 먼저 방문하고, 더 이상 방문할 수 있는 점이 없는 경우 종료한다. 정점 번호는 1번부터 N번까지이다.

#### 예제 입력

```python
4 5 1
1 2
1 3
1 4
2 4
3 4
```

- DFS : 1 > 2 > 4 > 3
- BFS : 1 > 2 > 3 > 4

#### 풀이

```python
# graph
N, M, V = list(map(int, input().split()))

graph = {i: [] for i in range(1, N+1)}
for node in range(M):
  	A, B = list(map(int, input().split()))
  	graph[A].append(B)
  	graph[B].append(A)
```

```python
# dfs
def dfs(graph, start_node, visited=[]):
    if start_node not in visited:
        visited.append(start_node)
        for next_node in sorted(graph[start_node]):
            dfs(graph, next_node, visited)
    return visited
```

```python
# bfs
def bfs(graph, start_node):
    visited, need_visit = [], [start_node]
    while need_visit:
        node = need_visit.pop(0)
        if node not in visited:
            visited.append(node)
            need_visit.extend(sorted(graph[node]))
    return visited
```

- DFS는 Recursive Function 방식으로 풀었는데, 시작 노드를 설정하면 다음 노드로 이어지게 하는 것이 좋은 방법이라 생각되어 위와 같이 작성했다.
- BFS는 공부했던 코드에서 `sorted`를 추가했는데, 조건에서 동등한 정점이 있을 경우에는 번호가 작은 것을 먼저 방문한다. 라고 되어 있어 정렬을 추가했다.

<br>

## 네트워크

> set()을 이용해 풀었고, 교집합이 있을 때 합집합하여 연결하도록 했다.

```python
def solution(n, computers):
    new_computers = []
    for computer in computers:
        new_computer = set()
        for i, connect in enumerate(computer):
            if connect: new_computer.add(i)
        new_computers.append(new_computer)
    
    for i in range(n):
        for j in range(n):
            if new_computers[i] & new_computers[j]:
                new_computers[i] = new_computers[i] | new_computers[j]
                new_computers[j] = new_computers[i] | new_computers[j]
    answer = []
    for computer in new_computers:
        if computer not in answer:
            answer.append(computer)
    return len(answer)
```

<br>

## 모든 경로 가져오기

> graph는 list 대신에 set으로 만들어 연결된 노드에 대해 add 시키자.

```python
def bfs_paths(graph, start, goal):
    queue = [(start, [start])]
    result = []

    while queue:
        n, path = queue.pop(0)
        if n == goal:
            result.append(path)
        else:
            for m in graph[n] - set(path):
                queue.append((m, path + [m]))
    return result
```


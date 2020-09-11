# Graph

> 난 그래프가 두렵다.

먼저 기억해야할 내용

* BFS (Breadth-First Search) : 너비 우선 탐색으로 정점들과 같은 레벨에 있는 노드 (형제 노드)를 먼저 탐색하는 방식으로 한 단계 씩 내려가며 해당 노드와 같은 레벨에 있는 노드를 먼저 순회하며 탐색한다.
* DFS (Depth-First Search) : 깊이 우선 탐색으로 정점의 자식들을 먼저 탐색하는 방식으로 한 노드의 자식을 끝까지 순회한 후 다시 돌아와 다른 형제들의 자식을 타고 내려가며 탐색

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

* DFS : 1 > 2 > 4 > 3
* BFS : 1 > 2 > 3 > 4



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

* DFS는 Recursive Function 방식으로 풀었는데, 시작 노드를 설정하면 다음 노드로 이어지게 하는 것이 좋은 방법이라 생각되어 위와 같이 작성했다.
* BFS는 공부했던 코드에서 `sorted`를 추가했는데, 조건에서 동등한 정점이 있을 경우에는 번호가 작은 것을 먼저 방문한다. 라고 되어 있어 정렬을 추가했다.





## Q. 가장 먼 노드 - Level 3

n개의 노드가 있는 그래프가 있고, 각 노드는 1부터 n까지 번호가 적혀있다. 1번 노드에서 가장 멀리 떨어진 노드의 개수를 구하려고 한다. 여기서 가장 멀리 떨어진 노드란 최단 경로로 이동 했을 때 간선의 개수가 가장 많은 노드를 의미한다.



#### 제한사항 :

* 노드의 개수 n은 2 이상 20,000 이하이다.
* 간선은 양방향이며 총 1개 이상 50,000개 이하의 간선이 있다.
* vertex 배열 각 행 [a, b]는 a번 노드와 b번 노드 사이에 간선이 있다는 의미이다.



#### 입출력 예 :

* `n = 6`
* `vertex = [[3, 6], [4, 3], [3, 2], [1, 3], [1, 2], [2, 4], [5, 2]]`

일 때, `return 3` 이다.




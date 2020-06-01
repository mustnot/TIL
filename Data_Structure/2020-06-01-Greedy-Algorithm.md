# 탐욕 알고리즘 (Greedy Algorithm)

- 탐욕 알고리즘은 최적의 해에 가까운 값을 구하기 위해 사용된다.
- 여러 경우의 수 중 **최적**이라 생각되는 해를 찾아 선택하는 방식으로 진행, 최종적인 값 도출

<br>

### 탐욕 알고리즘의 한계

- 탐욕 알고리즘은 근사치 추정에 활용되며, **반드시** 최적 해를 구할 수 있는 것은 아니다.
- 최적의 해의 **근사치**, 즉 최적의 해에 **가장 가까운 값**을 구하는 방법 중 하나로, 최적 해를 구하기 위해서는 보다 복잡한 로직이 필요하다.

<br>

### 문제 1 - 동전 문제

- 지불해야 하는 값이 4720원 일 때, 1원 / 50원 / 100원 / 500원 동전으로 **가장 적은** 동전의 수로 지불할 수 있는 방법은 무엇인가?

```python
# my code
coin_list = [500, 100, 50, 1]

def calc_coin_count(value):
	result = {}
	for coin in coin_list:
		coin_num = value // coin
		result[coin] = coin_num
		value -= coin * coin_num
	return result
```

```python
# study code
coin_list = [500, 100, 50, 1]

def min_coin_count(value, coin_list):
    total_coin_count = 0
    details = list()
    coin_list.sort(reverse=True)
    for coin in coin_list:
        coin_num = value // coin
        total_coin_count += coin_num
        value -= coin_num * coin
        details.append([coin, coin_num])
    return total_coin_count, details

```

<br>

### 문제 2 - 부분 배낭 문제 (Fractional Knapsack Problem)

- 무게 제한이 k인 배낭에 최대 가치를 가지도록 물건을 넣는 문제
- 각 물건은 무게(w)와 가치(v)로 표현된다.
- 물건을 조갤 수 있으므로, 물건의 일부분이 배낭에 넣어질 수 있음, 그래서 Fractional Knapsack Problem으로 불린다.

```python
# my code
data_list = [(10, 10), (15, 12), (20, 10), (25, 8), (30, 5)]

def solve_problem(data_list, limited):
    data_list = sorted(data_list, key=lambda x: x[1] / x[0], reverse=True)

    total_value, in_bags = 0, []
    for w, v in data_list:
        if limited - w >= 0:
            total_value += v
            limited -= w
            in_bags.append([w, v, 1])
        else:
            total_value += v/(w/limited)
            in_bags.append([w, v, 1/(w/limited)])
            break
    return total_value, in_bags
```

```python
# study code
data_list = [(10, 10), (15, 12), (20, 10), (25, 8), (30, 5)]

def get_max_value(data_list, capacity):
    data_list = sorted(data_list, key=lambda x: x[1] / x[0], reverse=True)
    total_value = 0
    details = list()
    
    for data in data_list:
        if capacity - data[0] >= 0:
            capacity -= data[0]
            total_value += data[1]
            details.append([data[0], data[1], 1])
        else:
            fraction = capacity / data[0]
            total_value += data[1] * fraction
            details.append([data[0], data[1], fraction])
            break
    return total_value, details
```
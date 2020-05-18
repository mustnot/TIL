# Hash Table

> 키(Key)에 데이터(Value)를 저장하는 데이터 구조로 Key를 통해 데이터를 받아올 수 있으므로, 속도가 획기적으로 빨라짐 - Python의 Dictionary 타입이 대표적인 예


![hash_table](https://user-images.githubusercontent.com/52126612/82210485-24aa6d80-994a-11ea-9b59-c9e806492d63.png)

## ✔용어 정리

1. 해쉬 (Hash) : 임의 값을 고정 길이로 변환하는 것
2. 해쉬 테이블 (Hash Table) : 키 값의 연산에 의해 직접 접근이 가능한 데이터 구조
3. 해싱 함수 (Hashing Function) : 키에 대해 산술 연산을 이용해 데이터 위치를 찾을 수 있는 함수
4. 해쉬 값 (Hash Value) 또는 해쉬 주소 (Hash Address) : 키를 해싱 함수로 연산해서, 해쉬 값을 알아내고, 이를 기반으로 해쉬 테이블에서 해당 키에 대한 데이터 위치를 일관성 있게 찾을 수 있음
5. 슬롯 (Slot) : 한 개의 데이터를 저장할 수 있는 공간
6. 저장할 데이터에 대해 키를 추출할 수 있는 별도 함수도 존재할 수 있음

## 1. Hash Table의 장단점과 주요 용도

- 장점
    - 데이터 저장/읽기 속도가 빠르다. (검색 속도가 빠르다.)
    - 해쉬는 키에 대한 데이터가 있는지(중복) 확인이 쉬움
- 단점
    - 일반적으로 저장 공간이 좀 더 많이 필요하다.
    - **여러 키에 해당하는 주소가 동일한 경우 충돌을 해결하기 위한 별도 자료 구조가 필요함**
- 주요 용도
    - 검색이 많이 필요한 경우
    - 저장, 삭제, 읽기가 빈번한 경우
    - 캐시 구현시 (중복 확인이 쉽기 때문에 사용)

## 2. 충돌(Collision) 해결 알고리즘 (좋은 해쉬 함수 사용하기)

### 1. Chaining 기법

- **개방 해싱** 또는 **Open Hashing 기법** 중 하나로 해쉬 테이블 저장공간 외의 공간을 활용하는 기법
- 충돌이 일어나면, 링크드 리스트를 사용해서 링크드 리스트로 데이터를 추가로 뒤에 연결시켜서 저장하는 기법

### 2. Linear Probling 기법

- **폐쇄 해싱** 또는 **Close Hashing 기법** 중 하나로 해쉬 테이블 저장공간 안에서 충돌 문제를 해결하는 기법
- 충돌이 일어나면, 해당 hash address의 다음 address부터 맨 처음 나오는 빈공간에 저장하는 기법
    - **저장 공간 활용도를 높이기 위한 기법**

## 연습 1 : 리스트 변수를 활용해서 Hash Table 구현

```python
hash_table = list([0 for i in range(8)])

def get_key(data):
	return hash(data)          # 문자를 정수로 리턴

def hash_function(key):
	return key % 8

def save_data(data, value):
	hash_address = hash_function(get_key(data))
	hash_table[hash_address] = value

def read_data(data):
	hash_address = hash_function(get_key(data))
	return hash_table[hash_address]	
```

## 연습 2 : Chaining 기법으로 충돌 해결 코드 추가

```python
hash_table = list([0 for i in range(8)])

def get_key(data):
    return hash(data)

def hash_function(key):
    return key % 8

def save_data(data, value):
    index_key = get_key(data)
    hash_address = hash_function(index_key)
    if hash_table[hash_address] != 0:    # 해당 키에 값이 1개 이상 존재할 경우,
        for index in range(len(hash_table[hash_address])):
            if hash_table[hash_address][index][0] == index_key:
                hash_table[hash_address][index][1] = value
                return
        hash_table[hash_address].append([index_key, value])
    else:    # 키에 값이 없을 경우 Array 형식으로 데이터 저장
        hash_table[hash_address] = [[index_key, value]]
    
def read_data(data):
    index_key = get_key(data)
    hash_address = hash_function(index_key)

    if hash_table[hash_address] != 0:
        for index in range(len(hash_table[hash_address])):
            if hash_table[hash_address][index][0] == index_key:
                return hash_table[hash_address][index][1]
        return None
    else:
        return None
```

## 연습 3 : Linear Probing 기법으로 충돌 해결 코드 추가

```python
hash_table = list([0 for i in range(8)])

def get_key(data):
    return hash(data)

def hash_function(key):
    return key % 8

def save_data(data, value):
    index_key = get_key(data)
    hash_address = hash_function(index_key)
    if hash_table[hash_address] != 0:
        for index in range(hash_address, len(hash_table)):    # 충돌이 일어나면, 빈 공간 탐색하여 저장
            if hash_table[index] == 0:
                hash_table[index] = [index_key, value]
                return
            elif hash_table[index][0] == index_key:
                hash_table[index][1] = value
                return
    else:
        hash_table[hash_address] = [index_key, value]

def read_data(data):
    index_key = get_key(data)
    hash_address = hash_function(index_key)
    
    if hash_table[hash_address] != 0:
        for index in range(hash_address, len(hash_table)):    # 키 값 말고 다른 공간에 있을 수 있어, 탐색하여 읽기
            if hash_table[index] == 0:
                return None
            elif hash_table[index][0] == index_key:
                return hash_table[index][1]
    else:
        return None
```

## 참고 : 해쉬 함수와 키 생성 함수

- 파이썬의 hash() 함수는 실행할 때마다, 값이 달라질 수 있음
- 해쉬 함수 :
    - SHA (Secure Hash Algorithm) : 안전한 해시 알고리즘

![sha](https://user-images.githubusercontent.com/52126612/82210441-0e9cad00-994a-11ea-924f-e5f1adae3352.png)


### SHA-1

```python
import hashlib

def get_key(data):
	hash_object = hashlib.sha1()
	hash_object.update(data.encode())
	return hash_object.hexdigest()

get_key('test')    # a94a8fe5ccb19ba61c4c0873d391e987982fbbd3
```

### SHA-256

```python
import hashlib

def get_key(data):
	hash_object = hashlib.sha256()
	hash_object.update(data.encode())
	return hash_object.hexdigest()

get_key('test')    # 9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08
```
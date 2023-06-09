# 그리디 알고리즘
= 탐욕법 : 현재 상황에서 지금 당장 좋은 것만 고르는 방법
-> 매 순간 가장 좋아 보이는 것을 선택하며, 현재의 선택이 나중에 미칠 영향에 대해서는 고려하지 않음
- 사전에 외우고 있지 않아도 풀 수 있는 가능성이 높음
- 정렬 알고리즘과 짝을 이뤄 출제되는 경우가 많음

## 거스름돈 예제
500원, 100원, 50원, 10원으로 N원을 거슬러 줄 때 동전의 최소 개수는? N은 항상 10의 배수임.
- 가장 큰 화폐 단위부터 돈 거슬러주기
```python
n = 1260
count = 0

money = [500, 100, 50, 10]

for m in money:
    # 거슬러 줄 수 있는 동전 개수
    count += n // m
    n %= m

print(count)
```

- 화폐의 종류가 K개라면 화폐의 종류만큼 반복을 수행하므로 시간 복잡도 : O(K)
- 거슬러 줘야하는 금액의 크기(n)와는 무관하고, 동전의 총 종류에만 영향을 받음

## 그리디 알고리즘의 정당성
- 최적의 해를 찾기 어려움
- 하지만 탐욕적으로 문제에 접근했을 때 정확한 답을 찾을 수 있다는 보장이 있을 때는 매우 효과적이고 직관적임
- 거스름돈 문제) 가지고 있는 동전 중에서 **큰 단위가 항상 작은 단위의 배수이므로 작은 단위의 동전들을 종합해 다른 해가 나올 수 없기 때문에** 그리디 가능
	- 동전의 단위가 배수 형태가 아니라 무작위라면 그리디로 해결 X
		- -> 다이나믹 프로그래밍
- 대부분의 그리디 알고리즘 문제에서는 문제 풀이를 위한 최소한의 아이디어를 떠올리고 이것이 정당한지 검토할 수 있어야 답을 도출할 수 있음
	- '10원짜리로만 모두 거슬러 주도록 코드를 작성하면 어떻게 되지?'
	- '10원짜리로만 모두 거슬러 주면 최적의 해를 구할 수 없겠구나'
	- '가장 큰 500원짜리부터 거슬러서 가장 작은 10원짜리까지 차례대로 거슬러 준다면 어떻게 될까?'
	- '큰 단위가 항상 작은 작은 단위의 배수 형태이므로, 이렇게 하면 항상 최적의 해를 보장할 수 있겠구나!'

### [백준](https://www.acmicpc.net/problem/11047)
---

## 큰 수의 법칙 문제
 다양한 수로 이루어진 배열이 있을 때 주어진 수들을 M번 더하여 가장 큰 수를 만드는 법칙(단, 배열의 특정한 인덱스(번호)에 해당하는 수가 연속해서 K번을 초과하여 더해질 수 없음)

- 가장 큰 수와 두 번째로 큰 수만 저장하면 됨
- 가장 큰 수 K번 더하고 두 번째로 큰 수를 한 번 더하는 연산 반복

```python
# 배열의 크기 N
# 숫자가 더해지는 횟수 M
# 연속해서 같은 인덱스를 K번 초과해서 더할 수 없음
# K <= M

from sys import stdin

input = stdin.readline

N, M, K = map(int, input().split())
arr = list(map(int, input().split()))
arr.sort(reverse=True) #내림차순 정렬

first = arr[0]
second = arr[1]

result = 0
while True:
    for i in range(K):
        if M == 0:
            break
        result += first
        M -= 1
    if M == 0:
        break
    result += second
    M -= 1
print(result)
```

- M이 100억 이상이라면? (시간 초과되지 않기 위해)
	- 반복되는 수열 이용
 	- 가장 큰 수가 더해지는 횟수 : ```int(M/(K+1))*K  +  M%(K+1)```

```python
# 배열의 크기 N
# 숫자가 더해지는 횟수 M
# 연속해서 같은 인덱스를 K번 초과해서 더할 수 없음
# K <= M

from sys import stdin

input = stdin.readline

N, M, K = map(int, input().split())
arr = list(map(int, input().split()))
arr.sort(reverse=True) #내림차순 정렬

first = arr[0]
second = arr[1]

# 가장 큰 수가 더해지는 횟수
count = int(M/(K+1)) * K + M % (K+1)

result = 0
# 가장 큰 수 더하기
result += first * count
# 두번째로 큰 수 더하기
result += second * (M-count)

print(result)
```

---

## 숫자 카드 게임 문제
여러 개의 숫자 카드 중에서 가장 높은 숫자가 쓰인 카드 한 장 뽑기
```
1. 숫자가 쓰인 카드들이 N x M 형태로 있음 
- N : 행의 개수
- M : 열의 개수
2. 먼저 뽑고자 하는 카드가 포함되어 있는 행 선택
3. 그다음 선택된 행에 포함된 카드들 중 가장 숫자가 낮은 카드를 뽑기
4. 따라서 처음에 카드를 골라낼 행을 선택할 때, 이후에 해당 행에서 가장 숫자가 낮은 카드를 뽑을 것을 고려하여 최종적으로 가장 높은 숫자의 카드를 뽑을 수 있도록 !
```

#### 아이디어
1. NxM 행렬을 반복문을 통해 행별로 리스트로 입력받기
2. 행별로 가장 작은 숫자 리스트에 담기
3. 그 리스트에서 가장 큰 숫자가 있는 인덱스(=행) 선택!

-> 각 행마다 가장 작은 수를 찾은 뒤에 그 수 중에서 가장 큰 수 찾기

```python
# NxM 입력 (1 <= N, M <= 100)
# N개의 줄에 걸쳐 자연수(1 이상 10000 이하) 입력받기
# 룰에 맞는 숫자 출력

from sys import stdin
input = stdin.readline

N, M = map(int, input().split())

result = 0
for _ in range(N):
    data = list(map(int, input().split()))
    # 각 행마다 가장 작은 수 찾기
    min_value = min(data)
    # 그 중 가장 큰 수 찾기
    result = max(result, min_value)

print(max_row)
```

### '1이 될 때까지' 문제
어떠한 수 N이 1이 될 때까지 다음 두 과정 중 하나를 반복적으로 선택해 수행하기
단, 두 번째 연산은 N이 K로 나누어떨어질 때만 선택

1. N에서 1을 뺀다
2. N을 K로 나눈다

#### 아이디어
- N이 K로 나누어 떨어지면 2번 연산
- 아니면 1번 연산
- N이 1이 되면 반복문 탈출

-> 최대한 많이 나누기

#### My code
```python
# N, K 입력 (N >= K)
# 연산의 최소 횟수 출력

from sys import stdin
input = stdin.readline

N, K = map(int, input().split())

count = 0
while True:
    if N == 1:
        break

    if N % K == 0:
        N /= K
    else:
        N -= 1
    count += 1

print(count)
```
-> 입력값이 커지면 시간이 오바됨

#### Clean code
```python
# N, K 입력 (N >= K)
# 연산의 최소 횟수 출력

from sys import stdin
input = stdin.readline

N, K = map(int, input().split())

result = 0
while True:
    # (N==K로 나누어떨어지는 수)가 될 때까지 1씩 빼기
    target = (N//K) * K   # target: 나눌 수 있는 가장 큰 수
    result += (N - target)
    N = target
    # N이 k보다 작을 때(더 이상 나눌 수 없을 때) 반복문 탈출
    if N < K:
        break
    result += 1
    N //= K

result += (N-1)
print(result)

```

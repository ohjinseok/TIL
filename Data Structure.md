# Data Structure

## 1. Stack

### 특성

* 자료를 쌓아 올린 형태의 자료구조
* 선형 구조(자료 간의 관계가 1대1)
* LIFO(Last-In-First-Out)

* 마지막 삽입된 원소의 위치를 top이라 부른다.

### 연산

* 삽입 : 저장소에 자료를 저장한다. push
* 삭제 : 저장소에서 자료를 꺼낸다. 삽입의 역순 pop
* 공백인지 확인 : isEmpty
* top에 있는 item 반환 : peek

![1549847557280](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1549847557280.png)

### Function call

* 마지막에 호출된 함수가 가장 먼저 실행을 완료하고 복귀하는 LIFO 구조이므로, LIFO 구조의 스택을 이용하여 수행순서 관리
* 함수 호출이 발생하면 호출한 함수 수행에 필요한 지역변수, 매개변수 및 수행 후 복귀할 주소 등의 정보를 stack frame에 저장하여 시스템 스택에 삽입
* 함수의 실행이 끝나면 시스템 스택의 top 원소를 삭제하면서 프레임에 저장되어 있던 복귀주소를 확인하고 복귀
* 함수 호출과 복귀에 따라 이 과정을 반복하며 전체 프로그램 수행이 종료되면 시스템 스택은 공백 스택이 된다.

![1549852775116](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1549852775116.png)

### Recursive call

**자기 자신을 호출하여 순환 수행되는 것**

프로그램의 크기를 줄이고 간단하게 작성할 수 있다.

#### factorial

```python
def fact(n):
    if n < 2:
        return 1
    return n * fact(n - 1)
```

![1549858446498](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1549858446498.png)

#### fibonacci

```python
def fibo(n):
    if n < 2:
        return n
    else:
        return fibo(n - 1) + fibo(n - 2)
```

$O(2^n)​$ 의 시간 복잡도

> 엄청난 중복 호출이 존재한다.(탐색 공간 완전 검색)
>
>  따라서 논리적 중복을 회피하고 줄여야 한다.

#### Memoization

이전에 계산한 값을 메모리에 저장해서 매번 다시 계산하지 않도록 하는 기술

메모리에 넣기(to put in memory 기억되어야 할 것)

```python
# memo를 위한 배열을 할당하고, 모두 0으로 초기화 한다
# memo[0]을 0으로 memo[1]는 1로 초기화 한다
def fibo1(n):
    global memo
    if n >= 2 and len(memo) <= n:
        memo.append(fibo1(n-1) + fibo1(n-2))
    return memo[n]

memo = [0, 1] 

memo = [] * 100
memo[0] = 0
momo[1] = 1

def fibo2(n):
    if n < 2 or not memo[n] :
        return memo[n]
    else:
        memo[n] = fibo2[n - 1] + fibo2[n -2]
        return memo[n]
```

### Dynamic Programming

DP 알고리즘은 그리디 알고리즘과 같이 최적화 문제를 해결하는 알고리즘이다.

입력 크기가 작은 부분 문제들을 모두 해결한 후에 그 해들을 이용하여 보다 큰 크기의 부분 문제들을 해결하여, 최종적으로 원래 주어진 입력의 문제를 해결하는 알고리즘

> 피보나치 수 DP 적용
>
> 1) 문제를 부분 문제로 분할한다.
>
> Fibonacci(n) 함수는 Fibonacci(n-1)과 Fibonacci(n-2)의 합
>
> Fibonacci(n-1)은 Fibonacci(n-2)와 Fibonacci(n-3)의 합
>
> Fibonacci(2)는 Fibonacci(1)과 Fibonacci(0)의 합
>
> Fibonacci(n)은 Fibonacci(n-1), Fibonacci(n-2), ... Fibonacci(2), Fibonacci(1), Fibonacci(0)의 부분집합으로 나뉜다.
>
> 2) 부분 문제로 나누는 일을 끝냈으면 가장 작은 부분 문제부터 해를 구한다.
>
> 3) 그 결과는 테이블에 저장하고, 테이블에 저장된 부분 문제의 해를 이용하여 상위 무제의 해를 구한다.
>
> ![1549860428933](assets/1549860428933.png)
>
> ```python
> def fibo2(n):
>     f = [0, 1]
>     
>     for i in range(2, n + 1):
>         f.append(f[i-1] + f[i-2])
>         
>     return f[n]
> 
> 	f = [] * 100
> 	f[0] = 0
>     f[1] = 1
> 	for i in range(2, n + 1):
>         f[n] = f[n-1] + f[n-2]
> ```

## 2. 비선형 자료구조

* Tree
* Graph

### 표현법

#### 인접행렬

![1549863966803](assets/1549863966803.png)

* 장점 : 구현이 쉽다.
* 단점 : 노드에 비해 간선의 수가 훨씬 적은 경우 연결된 노드의 수를 찾아볼 경우, 오랜 시간이 걸린다.

#### 인접 리스트

![1549863992991](assets/1549863992991.png)

* C++의 경우 vector를 사용하여 구현하면 편리함
* 장점 : 실제로 연결딘 노드들에 대한 정보만 저장하기 때문에 간선의 개수에 비례하는 메모리만 차지한다. 연결된 노드들을 확인하기 쉽다.
* 단점 : 노드 i와 j가 연결되어 있는지 확인하기 위해서는 i에 연결되어 있는 리스트를 전부 돌아야 한다.

### 순회

주어진 자료구조를 빠짐없이 조회하는 방법

#### 탐색 방법

##### 깊이 우선 탐색(DFS)

시작 정점의 한 방향으로 갈 수 있는 경로가 있는 곳까지 깊이 탐색해 가다가 더 이상 갈 곳이 없게 되면, 가장 마지막에 만났던 갈림길 간선이 있는 정점으로 되돌아와서 다른 방향의 정점으로 탐색을 반복하는 순회 방법

**Stack과 Visited 라는 자료 구조가 필요**

**stack**

* 사용자 정의 stack => 재귀에서 stack overflow가 발생할 것 같을 때 사용
* system 정의 stack : 재귀

1) 시작 정점 v를 결정하여 방문한다.

2) 정점 v에 인접한 정점 중에서

 - 방문하지 않은 정점 w가 있으면, 정점 v를 stack에 push하고 정점 w를 방문한다. 그리고 w를 v로 하여 다시 2)를 반복한다.
 - 방문하지 않은 정점이 없으면, 탐색의 방향을 바꾸기 위해서 stack을 pop하여 받은 가장 마지막 방문 정점을 v로 하여 다시 2)를 반복한다.

3) 스택이 공백이 될 때까지 2)를 반복한다.

```pseudocode
visited[], stack[] 초기화
DFS(v)
	v 방문;
	visited[v] <- true;
	do {
        if (v의 인접 정점 중 방문 안한 w 찾기)
        	push(v);
        while( w ) {
            w 방문;
            visited[w] <- true;
            push(w);
            v <- w;
            v의 인접 정점 중 방문 안한 w 찾기
        }
        v <- pop(stack);
	}while(v)
end DFS()
```

##### 너비 우선 탐색(BFS)

Queue를 사용한다.

#### 탐색 순서

##### pre-order

##### in-order

##### post-order


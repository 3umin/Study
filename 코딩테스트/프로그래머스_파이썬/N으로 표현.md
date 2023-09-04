## N으로 표현
- 목표 숫자 numbers를 숫자 N의 사칙연산으로만 유도해야 함
- 예를 들어 12를 5로 표현하는 경우는
  - 5 + 5 + (5/5) + (5/5)
  - 55/5 + (5/5)
  - (55 + 5) / 5
- 이런 경우들이 있음
- 최소한의 N으로 유도할 수 있는 numbers를 출력. 단 N이 8개가 넘어가면 -1을 출력

```python
def solution(N, number):
    num = [] # 숫자 조합을 담는 리스트
    
    for i in range(1, 9): # i = 숫자 N을 사용하는 횟수
        case = {int(str(N)*i)} # i번 사용하는 숫자 조합, 이어붙이는 경우의 수를 미리 넣어둠
        
        # 숫자 조합끼리의 사칙연산
        for j in range(0, i-1):  
            for x in num[j]:
                for y in num[-j-1]:
                    case.add(x+y)
                    case.add(x-y)
                    case.add(x*y)
                    if y != 0:
                        case.add(x//y)
               
        # 숫자 조합에 number 가 있는 경우
        if number in case:
            return i
        
        # 숫자 조합에 number 가 없으면 리스트에 추가
        num.append(case)
        
    return -1
```

## 해설
- 인터넷 보고 해결..
- N 2개의 경우 = N 1개의 경우 - N 1개의 경우 의 사칙연산
- N 3개의 경우 = N 2개의 경우 - N 1개의 경우 의 사칙연산
- N n개의 경우 = N n-1개의 경우 - N 1개의 경우 의 사칙연산

- 그래서 `for x in num[j]` 는 N 1개의 경우를 출력하고
- `for y in num[-j-1]` 은 뒤에서부터 불러오니 N n-1개의 경우를 출력

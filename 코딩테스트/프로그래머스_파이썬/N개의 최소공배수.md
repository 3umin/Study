## N개의 숫자로 구성된 리스트가 주어졌을 때, 이 숫자들의 최소 공배수 계산

```python
def solution(arr):
    from math import gcd
    answer = arr[0]
    for i in range(1, len(arr)):
        answer = (answer*arr[i]) // gcd(answer, arr[i])
    return answer
```

## 해설
- 혼자서 머리 쥐어뜯다가 도저히 생각이 안나서 구글링으로 해결...
- `gcd(a, b)`가 a b의 최대공약수를 구해주는 함수
- `arr` 리스트의 첫 두 원소의 곱에서 최대공약수를 나눈 몫이 두 숫자의 최소공배수
  - ex) 5, 40의 최소공배수는 5*40 = 200에서 최대공약수인 5를 나눈 40

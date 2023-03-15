## H-index를 계산하는 문제
- H-index : h번 이상 인용한 논문이 h번 이상인 h의 최댓값

```python
def solution(citations):
    h = 0
    for i in range(sorted(citations)[-1]+1):
        if (len(list(filter(lambda x: x >= i, citations))) >= i) & (i >= h):
            h = i
    return h
```

## 해설
- i를 citations 리스트에서 가장 큰 값까지 돌림
- citations 원소중에 i보다 큰 값의 길이가 i보다 크고, 직전의 h값보다 크다면 이를 h로 지정

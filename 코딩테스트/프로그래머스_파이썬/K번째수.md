## 정해진 범위 내의 리스트에서 K번째 수를 출력
- i, j, k 순서대로 주어지는 커맨드들
- 리스트에서 i~j까지의 수만 추려내고 k번째 수를 출력

```python
def solution(array, commands):
    answer = []
    print(array[4-1:4])
    for i, j, k in commands:
        ar_co = sorted(array[i-1:j])
        print(i, j, k)
        answer.append(ar_co[k-1])
    return answer

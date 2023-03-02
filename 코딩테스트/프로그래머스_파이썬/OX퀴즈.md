### 수식이 주어졌을 때 그 수식이 맞는 답인지를 판별하는 문제

```python
def solution(quiz):
    answer = []
    for x_list in quiz:
        x_split = x_list.split(' ')
        if x_split[1] == '-':
            if (float(x_split[0]) - float(x_split[2])) == float(x_split[4]):
                answer.append('O')
            else:
                answer.append('X')
        elif x_split[1] == '+':
            if (float(x_split[0]) + float(x_split[2])) == float(x_split[4]):
                answer.append('O')
            else:
                answer.append('X')
    return answer
```

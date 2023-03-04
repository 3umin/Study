## 수식이 주어졌을 때 그 수식이 맞는 답인지를 판별하는 문제

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

## 해설
- 숫자, 등호, 수식 등은 모두 띄어쓰기로 구분되어 있으므로 split
- split하면 string 형태로 값이 저장되므로 다시 float으로 변환
- '+', '-'만 수식에 존재해서 해당 경우만 조건식 적용

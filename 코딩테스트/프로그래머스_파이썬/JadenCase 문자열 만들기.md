## 주어진 문장을 JadenCase 단어로만 구성된 문자열로 바꾸기
- JadenCase 단어는 단어의 맨 앞 글자만 대문자이고, 나머지는 모두 소문자인 단어를 의미
- 숫자는 단어의 처음에만 올 수 있으며, 그 뒤에 나오는 문자는 소문자
- 알파벳, 숫자, 공백으로 구성(공백이 하나가 아닐 수도 있음)
- 모든 단어는 공백으로 구분


```python
def solution(s):
    list_word = s.lower().split(' ')
    for i in range(len(list_word)):
        if list_word[i] == ' ':
            pass
        elif len(list_word[i]) > 1:
            list_word[i] = list_word[i][0].upper() + list_word[i][1:]
        else:
            list_word[i] = list_word[i].upper()
        
    answer = ' '.join(list_word)
    return answer
```

## 해설
- 먼저 모든 글자를 소문자로 바꾼 후, 단어를 구분짓는 ' ' 공백자로 split
- 공백자가 하나가 아닐 수도 있으므로 공백값이 나오면 패스하고, 나머지는 글자 길이에 따라 결정

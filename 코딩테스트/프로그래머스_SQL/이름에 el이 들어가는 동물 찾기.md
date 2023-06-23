## 문제
- 이름에 el (대소문자 가리지 않음)이 들어가는 '개'를 찾기

```sql
SELECT ANIMAL_ID, NAME
FROM ANIMAL_INS
WHERE (ANIMAL_TYPE = 'Dog') & ((NAME LIKE '%El%')| (NAME LIKE '%el%')|(NAME LIKE '%eL%')|(NAME LIKE '%EL%')) 
ORDER BY NAME ASC
```

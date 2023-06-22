## 상위 n개 레코드
- 동물원에 있는 동물 중 가장 처음 들어온 동물의 이름을 뽑는 문제

```sql
SELECT NAME
FROM ANIMAL_INS
WHERE DATETIME = (SELECT MIN(DATETIME) FROM ANIMAL_INS)
```

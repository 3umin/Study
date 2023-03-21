# [CS224n] Lecture 3

# Neural Networds in NLP

## 1. Named Entity Recognition(NER, 개체명 인식)

인명, 지명 등 고유명사를 분류하는 방법

**분류기**를 실행해 각 고유명사를 인명(PER), 지명(LOC), 날짜(DATE) 등으로 분류

![3-2](https://user-images.githubusercontent.com/99728502/226566549-dbbcd149-5f24-4c6b-8984-18a8dff38ccd.png)

- 한계점
    1. 고유명사의 경계(Entity)를 정하기가 어려움
    2. 개체가 아닌지 알기가 어려움(ex. Future school → ‘school’, ‘future school’ 두개 중 판단)
    3. 개체 분류가 모호하며 문맥에 의존, 절대적 기준 x

## 2. Binary word Window Classification

중심단어와 주변단어들(context)을 함께 분류문제에 활용

ex : Not all [museumns in **Paris** are amazing] / window : [ ], 중심단어 : Paris

![3-1](https://user-images.githubusercontent.com/99728502/226566575-fc72e87e-8320-4221-8fd6-06c606872e99.png)

Window 길이를 2로 설정하여 Paris에서 앞뒤로 2개씩 총 5개의 word vectors를 합쳐서 5d 크기의 벡터 형성

그리고 이 벡터를 활용해 classifier 제작

이 상황에선 앞에 전치사가 있는 것을 보니 파리는 지명일 확률이 높아보임

## Jacobian Matrix: Generalization of the Gradient

- **Jacobian Matrix**

![3-3](https://user-images.githubusercontent.com/99728502/226566602-ae118e18-d5b6-4d71-8a53-59ff17990ed2.png)

![3-4](https://user-images.githubusercontent.com/99728502/226566609-660511bc-4cfe-431a-a829-bd8258f0f854.png)


- **Chain Rule**

![3-20](https://user-images.githubusercontent.com/99728502/226566637-dcbc9285-e310-4244-9a5a-954ff3acda6a.png)

z의 함수인 h를 z에 대해 미분하는 방법

- **Example Jacobian** : Elementwise activation Function
1. 등식을 간단한 조각으로 세분화
    
    h안에 들어가는 식 Wx + b = z로 치환
    
![3-7](https://user-images.githubusercontent.com/99728502/226566675-88efb2cd-125e-4132-bf39-f6a869b0442f.png)

![3-8](https://user-images.githubusercontent.com/99728502/226566701-2c51bc33-8e91-44fb-9ec1-c07298797ab9.png)

1. Chain Rule 적용

![3-12](https://user-images.githubusercontent.com/99728502/226566722-65de15b3-91d1-442b-b76c-61ab58107d8e.png)

1. Jacobian 활용
    
    여태까지 계산한 내용을 대입
   
![3-9](https://user-images.githubusercontent.com/99728502/226566746-faa20a17-3955-4a96-888f-88adf0ebd620.png)

![3-10](https://user-images.githubusercontent.com/99728502/226566783-b18001a6-9e8e-462d-a74c-9fb4497ead0b.png)


1. 다른 변수에 대해 다시 계산 실시

![3-11](https://user-images.githubusercontent.com/99728502/226566807-475a1aee-48c4-4a71-92d6-12e569920a75.png)

![3-14](https://user-images.githubusercontent.com/99728502/226566836-6900772b-e309-4036-99bf-7c31ce16f772.png)

$\delta :$ 오류 신호

${\partial s\over \partial W} = \delta{\partial z \over \partial W}$,  $z = Wx + b$

⇒ ${\partial s \over \partial W} = \delta^Tx^T$

Forward progation: 4개의 다른 인풋과 아웃풋을 계산하는것(검은색)

## Forward Propagation(순전파)

4개의 다른 input과 output을 순서대로 계산하는 것(아래 이미지에서 검은색부분)

![3-16](https://user-images.githubusercontent.com/99728502/226566859-fcb9b0fb-881a-4262-902e-c2031035a797.png)


## BackProgation(역전파)

output 벡터를 weight matrix에 대해 미분한 뒤 output 결과로부터 input 값을 추론(파란색)

즉 최종적으로 나온 손실함수(비용함수)의 값을 바탕으로 모델의 파라미터들을 확인

→ 더 좋은 파라미터를 선택할 수 있음

- Single Input

![3-17](https://user-images.githubusercontent.com/99728502/226566876-1ccad6cb-8d9f-455c-94cf-2f36d2cbd422.png)

- Multiple Input

![3-18](https://user-images.githubusercontent.com/99728502/226566897-3c91f10a-81e5-4791-a0ef-d2eb24b90c50.png)

## Computation Graph

값을 그래프 노드 위에 위치시켜 그래프 기반 연산 → 효율적

![3-19](https://user-images.githubusercontent.com/99728502/226566931-448c36a7-50f9-44ce-ba75-775e180eb658.png)

### Forward : Topological sort로 정렬한 뒤 노드를 지나감

- 이전의 값을 바탕으로 노드의 값을 계산

### Backward

- Output 노드에서 gradient = 1로 시작
- Topological sort의 반대 순서로 노드를 지나며 이어지는 노드에 대해 local gradient 계산

### Numerical Gradient

$f'(x) \approx {f(x+h) - f(x-h)\over 2h}$

- 미분 공식을 통해 gradient 쉽게 계산 가능
- 하지만 적용할 때 마다 매번 새로 연산해야 하기 때문에, Computation graph 방식에 비해 느림

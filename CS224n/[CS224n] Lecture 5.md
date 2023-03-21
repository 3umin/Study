# [CS224n] Lecture 5

# Neural Dependency Parsing

<img width="439" alt="5-1" src="https://user-images.githubusercontent.com/99728502/226567889-669a7023-11f3-4a26-af51-e80c2f98b79d.png">

- 인공신경망을 활용해 Parsing을 하는 방법
- 각각의 단어를 d 차원의 벡터로 표현
- 비슷한 단어들은 벡터간의 거리가 가깝게 표현됨
- word vector 이외에도, **Part-Of-Speech tags(POS)**와 **Dependency labels** 또한 d 차원의 벡터

## 딥러닝 분류기: 비선형 분류기

전통적인 머신러닝 분류기는 그렇게 효과가 뛰어나지 않음 - **선형성**을 기반으로 영역을 나눔

<img width="328" alt="5-2" src="https://user-images.githubusercontent.com/99728502/226567967-da39369b-05f7-4f55-a663-961bb1d14f6b.png">


- 신경망은 비선형 관계를 나타낼 수 있음
- 분류 결정은 softmax에 의해 선형적으로 결정되지만, 원본 데이터에선 비선형으로 나타남

# Neural dependency parser model architecture

## Simple feed-forward Neural Network Multi-Class Classifier

<img width="419" alt="5-3" src="https://user-images.githubusercontent.com/99728502/226568002-7feea73d-6d46-4393-a2bb-29254af8b37d.png">

- 로그 손실함수는 이전과 같이 역전파 되어 input 값을 계산하는 용으로 사용

## Trasition-Based Parser의 대안

### Graph-Based Dependency Parser

<img width="335" alt="5-4" src="https://user-images.githubusercontent.com/99728502/226568024-1d07f01e-0e10-445c-8b98-7332aeab4ef0.png">

- 각각의 단어에 대해 모든 가능한 Dependency의 점수를 계산
- 최근엔 Graph-Based Parser와 Neural Network가 결합된 **Neural Graph-Based Parser** 활용

# A bit more about Neural Networks

<img width="485" alt="5-5" src="https://user-images.githubusercontent.com/99728502/226568041-19958630-488c-4703-aa48-96b5d9609ad8.png">

<img width="245" alt="5-6" src="https://user-images.githubusercontent.com/99728502/226568057-735b8626-5478-49c3-be98-c1e1483b5154.png">

- 과거: 손실함수에 대해 Regularization → 과대적합(Overfitting) 방지
- 현재: Regularization은 ‘Big’ model에 대해 시행했을 때 잘 이루어짐
    
             훈련셋에 대해 과대적합이 심하게 진행되었더라도, 신경쓰지 않는다
    
        Big Neural Net은 항상 훈련셋에 과대적합되어 있기 때문
    

## Dropout 테크닉

- Feature 사이에 동시적응(co-adaptation)을 방지하기 위해 사용
- 훈련의 각 인스턴스 혹은 각 배치를 할 때, 모델에 있는 각각의 neuron에서 인풋값의 절반을 제거

- 신경망 모델에 대해 Regularizton을 하는 가장 좋은 방법으로 여겨졌음
- 현재는 일부 특성은 더 강하게 Regularize되고, 일부는 아니게 되는 Feature-dependent로 여겨짐

## Non-Linearities, Old and New

<img width="499" alt="5-7" src="https://user-images.githubusercontent.com/99728502/226568087-2a772e7d-598f-4e86-b466-4514d0ac184e.png">

- 과거
    - 가장 Classic한건 Logistic(sigmoid)
    - Logistic의 단점은 항상 0~1사이 양수 공간에서만 움직임 → tanh 사용
    - 여전히 많은 곳에서 Logistic과 tanh는 쓰이지만, 더이상 딥러닝 기법의 디폴트가 아니게 됨

- 현재
    - hard tanh → 일정 범위를 넘어가면 값이 항상 동일해서 정보를 얻을 수 없을 것처럼 보이지만, 경험적으로 이러한 모델이 매우 유용했음
    - 그리고 가장 최근에 가장 성공적이고 비선형쪽에서 가장 널리 활용되는 것이 ReLU
        - 가장 간단한 비선형관계 → 빠른 훈련 가능 및 역전파하기 수월해서 좋은 효과
            
            

## Parameter 초기화

- 모든 weights를 균등하게 -r부터 r까지 초기화함(수치가 너무 작거나 커지지 않게 하기 위함)
- 0부터 시작하면 모든게 대칭이 되므로 0부터 시작하지 않음

## Optimizers

- 일반적으로, 간단한 SGD(Stochastic Gradient Descent)가 좋은 효과를 나타남
- Learning rate 조정 : Learning rate를 매 k번째마다 절반으로 줄임

# Language Modeling

<img width="370" alt="5-8" src="https://user-images.githubusercontent.com/99728502/226568122-91777412-0e05-4ba1-9699-eebd49f2bc0e.png">

- 다음 단어로 무엇이 올 지 예측하는 것
- $P(x^{(t+1)} | x^{(t)}, ... \ ,x^{(1)})$ 의 계산을 통해 예측

## Assigns Probability to a Piece of Text

<img width="331" alt="5-9" src="https://user-images.githubusercontent.com/99728502/226568151-97350ea2-5427-4510-ba0d-136a461b31df.png">

다음 단어를 추천해주는 것이 가장 흔한 예시(구글 검색엔진에서 자동완성도 포함)

## N-Gram Language Models

<img width="411" alt="5-10" src="https://user-images.githubusercontent.com/99728502/226568178-1f52b0f8-edf0-4c96-8140-51e7569815cc.png">

<img width="541" alt="5-11" src="https://user-images.githubusercontent.com/99728502/226568199-3f7aca07-b9f9-4f7d-bfe2-c76b7cd3aa2c.png">


- 서로 다른 n-gram의 빈도에 대한 통계를 수집해서 다음 단어를 예측
- 전제 : $x^{(t+1)}$는 앞서 있는 n-1개의 단어들에만 영향을 받음
- 각각의 확률은 전체 text에 대해 빈도를 세어서 계산  $\approx \ {count(x^{(t+1)}, x^{(t)}, ... \ , x^{(t-n+2)})\over count(x^{(t)}, ... \ , x^{(t-n+2)})}$

Ex) Suppose 4-gram language model

<img width="437" alt="5-12" src="https://user-images.githubusercontent.com/99728502/226568222-085202e9-139e-481b-832b-9f0faf3d1f5f.png">

예를 들어,

‘students opend their’이 1000번 발생

‘students opend their books’이 400번 발생

 → $P(books| students \ opened \ their)$ = 0.4

‘students opend their exams’이 100번 발생

 → $P(exams |students  \ opened \ their)$ = 0.1

### N-gram Language model의 문제점

1. 희소성 문제
- 위의 예시에서, 만약 ‘students opend their’ 뒤에 단어가 한번도 나타나지 않는다면? → 확률은 0
    
    ⇒ 해결방법 : Smoothing(모든 단어의 빈도에 대해 약간의 노이즈 델타를 더해줌)
    
- 만약 ‘students opend their’ 자체가 한번도 나타나지 않는다면? → 확률을 계산할 수 없음
    
    ⇒ 해결방법 : Backoff(’students opened their’ 대신에 ‘opened their’을 조건으로 두고 계산)
    
- n이 커질수록 희소성 문제는 더욱 더 심해짐(보통 n >5 일 경우 심각)

1. 저장공간 문제
- n-gram 전체의 빈도를 전부 저장해야되기 때문에, 용량이 너무 커짐

1. 모순성 문제
- n-gram model이 문맥을 충분히 반영하지 못하는 문제
- '다음 단어는 오직 직전의 (n-1)개의 단어에만 영향을 받는다.' 라는 가정 때문에 이전 문맥을 
충분히 반영하지 못함
→  n의 크기를 늘리면 이러한 문제를 어느정도 해결할 수 있지만 동시에 희소성이 심화됨

## Neural Language model

- Fixed-window Neural Language Model

<img width="488" alt="5-13" src="https://user-images.githubusercontent.com/99728502/226568255-015c13df-6f76-4136-abd5-0fc6a86e4a7a.png">

- n-gram 모델의 고질적인 문제를 해결하기 위해 등장
- 3강에서 다루었던 NER의 window-based neural model을 기반으로 진행
- n-gram 모델과 마찬가지로, 다음 단어를 예측하기 위해 더 큰 크기의 텍스트가 필요한 한계점
- ‘the students opened their’로 학습한 모델에 다른 새로운 단어(student → pupil)를 사용할 때,
    
    n-gram은 어떻게 확률을 계산할지에 대한 정의가 없음
    
    Neural language model은 pupil이 student랑 비슷한 의미를 가지니까 그대로 예측 가능
    

- 장점
    - 희소성 문제로부터 해결
    - 모든 n-gram의 정보를 저장할 필요가 없음
- 문제점
    - Fixed window의 사이즈가 너무 작음
    - 윈도우 사이즈를 늘리더라도 중요한 문맥을 포함하지 않을 수 있음
    - 단어의 위치에 따라 가중치가 다르기 때문에 같은 단어에 대해서도 여러번의  학습 수행

# Recurrent Neural Networks(RNN)

<img width="430" alt="5-14" src="https://user-images.githubusercontent.com/99728502/226568280-feb416e7-d54c-4aaa-82b5-f39153aded98.png">

- Hidden layer(Hidden states)가 존재
- 첫번째 단어로부터 hidden states를 계산하고, 두번째 단어를 예측할 때 두번째 단어 뿐 아니라, 첫번째 단어로부터 계산한 hidden states값도 영향을 줌
    
    ⇒ 이전에 있던 단어가 hidden layer을 통해 다음 단어에 영향을 줌
    

## Simple RNN language model

<img width="412" alt="5-15" src="https://user-images.githubusercontent.com/99728502/226568314-da2f26e1-e578-402d-8e6c-0a3257cc33b7.png">

- 장점
    1. 이론상으로는 input length에 상관없이 다음 단어를 예측할 수 있음
    2. 먼 곳에 위치한 단어도 고려할 수 있어 context를 반영할 수 있음
    3. input이 길어져도 model size가 증가하지 않음

- 단점
    1. 다음 단계로 진행하기 위해서는 이전 단계의 계산이 완료되어야 하므로 계산이 병렬적으로 진행되지 않아 느림
    2. 이론적으로는 먼 곳의 단어를 반영할 수 있지만 실제로는 Vanishing gradient등의 문제가 있어 context가 반영되지 않는 경우도 있음
